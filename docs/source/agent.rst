Shepherd agent
==============

Once your Shepherd user is created, you can add a Shepherd agent associated to that user. In the Shepherd framework, a Shepherd agent and an RL agent are not the same thing: a Shepherd agent can use several RL agent instances. When launched, a Shepherd agent loads its latest save, which is the model saved by the latest RL agent instance learning on the environment.


Under one Shepherd agent, several instances of RL agents can learn in parallel in the environment, and share their knowledge with each other to improve their individual performance. This means that whenever a new RL agent instance is added to the RL agent's pool of a Shepherd agent, its learning can immediateley be kickstarted using other Rl agents' latest learned model. This allows for fresh RL agents to behave in a much less random fashion than if learning from scratch.


API keys
--------

To avoid the need for a user name and password, we use generated **API keys** by the UUID module. The particularity of our usage of API keys is that one API key is also associated to one Shepherd Agent. When logging in to Shepherd, the client sends an API key to the server, which retrieves  the corresponding API key object, linked to the user's corresponding Agent. In the case where a user has multiple Shepherd agents, they would also have multiple API keys, one per agent. The advantage of this setting is that if one API key is leaked,  only one of the user's Shepherd Agents is leaked.


Creating a Shepherd agent
-------------------------


.. image:: pictures/admin_site.png
    :width: 1000
    :alt: Alternative text

The user owning the Shepherd agent must be specified. Only they (the user) can launch that specific Shepherd agent, access its learned models, see its learning curves, perform special actions such as deleting the agent  and its models, etc.

The algorithm run by all RL agent instances under that Shepherd agent must also be specified. There exist several RL algorithms, and some might work better than others depending on the problem/environment (e.g., not all RL algorithms are compatible with continuous actions). The algorithm chosen during the creation of a Shepherd agent cannot be changed afterwards.

.. image:: pictures/user_and_algo_of_agent.png
    :width: 1000
    :alt: Alternative text


Action and observation spaces
-----------------------------

A Shepherd is always associated with one environment to be solved. This environment, run on the client's side, must send observations to the Shepherd agent, and receives actions in return.

The environment we are going to tackle in this example is CartPole-v0, from the Gym. Its observations are in the form of vectors of four floats; each float is equal to 3.4028234663852886e+38 at its highest, and -3.4028234663852886e+38 at its lowest.


.. image:: pictures/cartpole.png
    :width: 1000
    :alt: Alternative text

In the specs of the Shepherd agent on the admin site, the action and observation spaces must be expressed in valid JSON.  We set the action space to two (corresponding to the two discrete actions available in CartPole); the observation space can eb expressed as follows: [[space], low, high].


.. image:: pictures/action_and_obs.png
    :width: 1000
    :alt: Alternative text




Download latest model
---------------------
Download zip button.


.. image:: pictures/special_actions.png
    :width: 1000
    :alt: Alternative text


When a user wants to launch an RL algorithm on their environment, a new Shepherd **Agent** object belonging to that user (and no one else) is added to the database. The specification of the action and observation spaces of the user's environment are required for the initialization of a Shepherd Agent. New Shepherd agents are created through the admin page, as well as the parameter values to configure the algorithm run by an agent. The difference between a Shepherd agent and a standard RL agent is that several standard RL agents can ``run" one Shepherd agent: a Shepherd agent stores a configuration, specifying which algorithm must be executed, the parameters, as well as the latest generated model and results obtained. There are few ``special actions" that the user can do through the admin page;  they can download data from the agent, such as the episode rewards and the agent's model, and reboot its learning, which is especially useful when the environment has been modified and that previous results become irrelevant.


Episode Returns
===============

Multiple **EpisodeReturns** are associated to one Shepherd Agent. Each EpisodeReturn object stores a float (the sum of all rewards collected during one episode), and a date time field. Episode returns are used to plot the Agent's learning curve, automatically displayed on the admin page.




