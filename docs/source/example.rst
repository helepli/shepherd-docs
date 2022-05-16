Example of a Shepherd client
============================

The example code below logs in our Shepherd server using an API key. Once successfully logged, the code starts communicating with the shepherd/env/ view. Each of these communications must contain four information: an observation *obs*, a *reward*, a *done* flag and a dictionary *info*. All four information come from the user's environment. The observation shoudl contain whatever information the agent should see to be able to learn the task; if the agent must control the setting of a smart thermostat, the observation could be the current room temperature and the current target temperature of the thermostat. The reward is a floating-point value; it is used to indicate to transmit to the agent an idea of how good its last action(s) was(were). In the thermostat example, the user of the thermostat could push a green or red button to indicate whether he is happy with the current temperature of the room, and the green button could correspond to a 1.0 sent to the server as reward, the red button as a 0.0.

The done flag indicates the end of an episode, and the start of a new one. An episode could end every 24 hours in the thermostat example. Info can remain an empty dictionary, and is empty in most RL applications. When an episode ends an that a new one starts, the environment often executes some sort of preparation for the next episode. In our example below, the CartPole environment from Gym explicitly calls a function called *reset()*, but it it does not need to be called that way. Typically, a reset procedure puts the environment back in an initial state, as when putting all pieces back on the board when preparing to play a new game of chess.


For each *obs*, *reward*, *done*, *info* communication from the client to the server, the server responds with an action from the agent. An action can be an integer, a float or a even a vector of several values, depending on teh task to be solved. In the example of the thermostat, the agent's action could be a target temperature. The agent does not choose an action when *done* is True, but returns None instead. If the action received from the server is None, then the last *done* received by the server was True, meaning that an episode just ended, and that the agent did not choose any action.



.. code-block:: python

    import os
    import sys
    import json
    import requests

    import gym

    def send_json_to_website(d, path):
        """ Sends "d", a dictionary, to the website, and returns the response as a Python dictionary
        """
        r = requests.post('http://localhost:8000/'+ path, json=d)
        return r.json()

    # Make the environment on client side (here, gym environment)
    env = gym.make('CartPole-v0')
    obs = env.reset()

    # First communication: login user
    ok = send_json_to_website({'apikey': your_api_key}, 'shepherd/login_user/')

    if 'ok' in ok:
        print("Login successful")
        session_key = ok['session_key']

        # Start sending observations, in exhange of actions with the agent on the server side
        action = send_json_to_website({'obs': obs.tolist(), 'reward': 0.0, 'done': False, 'info': {}, 'session_key': session_key}, 'shepherd/env/')

        while True:
            print("action ", action)
            obs, reward, done, info = env.step(action['action'])
            action = send_json_to_website({'obs': obs.tolist(), 'reward': reward, 'done': done, 'info': {}, 'session_key': session_key}, 'shepherd/env/')

            # End of an episode, beginning of a new one
            if action['action'] is None:
                assert(done)
                obs = env.reset()
                action = send_json_to_website({'obs': obs.tolist(), 'reward': 0.0, 'done': False, 'info': {}, 'session_key': session_key}, 'shepherd/env/')

        # When the client wants to stop, its last communication is to return None as observation
        action = send_json_to_website({'obs': None}, 'shepherd/env/')

    else:
        print("ERROR: Could not login to server")




On the Shepherd server side
===========================

In a conventional reinforcement learning setting, the agent is the driving force of the interactions between the agent and the environment. In a typical RL workflow, the agent generates an action, then prompts the environment for an observation, in exchange of that action. In our Shepherd framework, on the other hand, it is the environment on the client side that prompts the agent on our server for an action, in return of an observation. This role reversal calls for a carefully though out solution on the server side for standard RL algorithms to smoothly adapt to that modified communication workflow.

To leverage their Shepherd agent running on our server, users must first log in by sending their API key. Once the Shepherd agent linked to the particular API key received by the server is retrieved, a thread is created to run an execution of that Shepherd agent; the initialization of a thread looks for the latest save of any previous execution of the agent, to pick up where the last one ended. If one such save is found (i.e., a zip file in which neural networks weights have been saved), the thread loads it, otherwise, the thread starts learning from scratch. In any case, each running thread regularly saves its network weights in a directory shared by all executions of a given Shepherd Agent. The thread initialization also takes care of instantiating the Shepherd Environment (called ShepherdEnv-v0), with which the learning execution is actually going to interact.

Several threads can run the same Shepherd agent at the same time, as in the case of a web application allowing multiple users to log using the same API key. Although each user technically trains its own instance of the RL algorithm specified by the Shepherd agent, since threads advise each other using a transfer learning method, and share their saves, it is as if all users train the same Shepherd agent.

Once the User and the Agent have been identified, the user's client can start sending observations from their environment. All messages sent from the client to the server must not only contain an observation, but also a reward and a Boolean **done** indicating whether the episode is over or not.
This information is stored by the targeted learning execution (or thread) immediately after have been received on the server end. Each thread has two queues as attributes: an observation queue, storing the latest information received from the client, and an action queue, in which the learning execution puts its actions. When a conventional RL algorithm (such as the ones in Stable Baselines 3) running on the server prompts the environment for an observation, reward and done information, the thread prompts the ShepherdEnv-v0 environment with the action selected as input. This custom gym environment puts the action in the thread's action queue, dequeues the thread's observation queue, and returns the output of the observation queue. To standard RL algorithms, ShepherdEnv-v0 ``feels" just like a regular gym environment, although it does not simulate the user's environment; the environment actually being learned by the agent is running on the client side. ShepherdEnv-v0 merely manages the communication between the client's environment and the RL algorithm running on the server.
