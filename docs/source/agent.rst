Shepherd agent
==============

Once your Shepherd user is created, you can add a Shepherd agent associated to that user. In the Shepherd framework, a Shepherd agent and an RL agent are not the same thing: a Shepherd agent can use several RL agent instances. When launched, a Shepherd agent loads its latest save, which is the model saved by the latest RL agent instance learning on the environment.


Under one Shepherd agent, several instances of RL agents can learn in parallel in the environment, and share their knowledge with each other to improve their individual performance. This means that whenever a new RL agent instance is added to the RL agent's pool of a Shepherd agent, its learning can immediateley be kickstarted using other Rl agents' latest learned model. This allows for fresh RL agents to behave in a much less random fashion than if learning from scratch.


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





Delete latest (and all) model(s)
--------------------------------
Delete zip button


Delete learning curve
---------------------

Restart agent
-------------

Add API key
-----------


