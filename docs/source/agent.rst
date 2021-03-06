Shepherd agent
==============

New users are added to the database by an internal staff member through the admin page. The onfly information required is a name to identify the user; we use API keys to log users in instead of a user password. A User can possess more than one Shepherd Agent (one per environment that they have, or one per different algorithm on one environment to compare algorithms, for instance).

Once a Shepherd user exists, a Shepherd agent associated to that user can be created. To avoid the need for a user name and password, we use generated **API keys** by the UUID module. The particularity of our usage of API keys is that one API key is also associated to one Shepherd Agent. When logging in to Shepherd, the client sends an API key to the server, which retrieves  the corresponding API key object, linked to the user's corresponding Agent. In the case where a user has multiple Shepherd agents, they would also have multiple API keys, one per agent. The advantage of this setting is that if one API key is leaked, only one of the user's Shepherd Agents is leaked.

The specification of the action and observation spaces of the user's environment are required for the initialization of a Shepherd Agent. It is also at the agent creation time that an RL algorithm to be executed by the agent is chosen. New Shepherd agents are created through the admin page, as well as the parameter values to configure the algorithm run by an agent.

The difference between a Shepherd agent and a standard RL agent is that several standard RL agents can "run" one Shepherd agent: a Shepherd agent stores a configuration, specifying which algorithm must be executed, the parameters, as well as the latest generated model and results obtained.


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

Let's consider CartPole-v0, from the Gym. Its observations are in the form of vectors of four floats; each float is equal to 3.4028234663852886e+38 at its highest, and -3.4028234663852886e+38 at its lowest.


.. image:: pictures/cartpole.png
    :width: 1000
    :alt: Alternative text

In the specs of the Shepherd agent on the admin site, the action and observation spaces must be expressed in valid JSON.  We set the action space to two (corresponding to the two discrete actions available in CartPole); the observation space can eb expressed as follows: [[space], low, high].


.. image:: pictures/action_and_obs.png
    :width: 1000
    :alt: Alternative text

There are few "special actions" that the user can do through the admin page;  they can download data from the agent, such as the episode rewards and the agent's model, and reboot its learning, which is especially useful when the environment has been modified and that previous results become irrelevant.

.. image:: pictures/special_actions.png
    :width: 1000
    :alt: Alternative text



Algorithms
----------


An **Algorithm** has a fairly brief definition in our database; it has a name, often the abbreviated version of the name of an RL algorithm, such as PPO or A3C and a Boolean flag indicating whether it is compatible with environments with continuous actions or not. An Algorithm also has several **Parameters** associated to it. Algorithm objects are preexisting the creation of Users and Agents; we pre-populate the database with RL algorithms from Stable Baselines 3, but adding new algorithms that are not from Stable Baselines 3 is trivial.



Parameter values
----------------

When a new Shepherd agent is created, an RL algorithm must be chosen to be run by this agent. If they want to, the user can configure the algorithm through setting the value of some parameters, via the addition of **ParameterValue** objects in the database. This comes in handy when the user wants to try parameter values different from predefined default ones. The value chosen is stored in value_int, value_float, value_bool (exclusive) or value_str, depending on the *type* of the corresponding **Parameter** object (see below); all three other value attributes are set to null. A ParameterValue is associated to one Shepherd Agent, and to one Parameter.


Parameters
----------


Each RL algorithm in the database uses a multitude of **Parameters** (e.g., learning rate, batch size, gamma, etc), all associated to that one algorithm. The type of the value this parameter can take is defined by an Integer in [1, 2, 3, 4]; if *type* is equal to 1, the value of the parameter must be an Integer, if *type* = 2, the value must be a floating point, etc. Only one of the attributes value_int, value_float, value_bool and value_str contains a value different than null, depending on the *type* attribute.  Similarly to Algorithms, Parameters are pre-existing ParameterValues; we populate the database with parameters used by most RL algorithms, and set their default values, before the addition of Users and their Shepherd Agents.



Episode Returns
---------------

Multiple **EpisodeReturns** are associated to one Shepherd Agent. Each EpisodeReturn object stores a float (the sum of all rewards collected during one episode), and a date time field. Episode returns are used to plot the Agent's learning curve, automatically displayed on the admin page.




