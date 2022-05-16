Algorithms
==========


An **Algorithm** has a fairly brief definition in our database; it has a name, often the abbreviated version of the name of an RL algorithm, such as PPO or A3C and a Boolean flag indicating whether it is compatible with environments with continuous actions or not. An Algorithm also has several **Parameters** associated to it. Algorithm objects are preexisting the creation of Users and Agents; we pre-populate the database with RL algorithms from Stable Baselines 3, but adding new algorithms that are not from Stable Baselines 3 is trivial.



Parameter values
----------------

When a new Shepherd agent is created, an RL algorithm must be chosen to be run by this agent. If they want to, the user can configure the algorithm through setting the value of some parameters, via the addition of **ParameterValue** objects in the database. This comes in handy when the user wants to try parameter values different from predefined default ones. The value chosen is stored in value_int, value_float, value_bool (exclusive) or value_str, depending on the *type* of the corresponding **Parameter** object (see below); all three other value attributes are set to null. A ParameterValue is associated to one Shepherd Agent, and to one Parameter.


Parameters
----------


Each RL algorithm in the database uses a multitude of **Parameters** (e.g., learning rate, batch size, gamma, etc), all associated to that one algorithm. The type of the value this parameter can take is defined by an Integer in [1, 2, 3, 4]; if *type* is equal to 1, the value of the parameter must be an Integer, if *type* = 2, the value must be a floating point, etc. Only one of the attributes value_int, value_float, value_bool and value_str contains a value different than null, depending on the *type* attribute.  Similarly to Algorithms, Parameters are pre-existing ParameterValues; we populate the database with parameters used by most RL algorithms, and set their default values, before the addition of Users and their Shepherd Agents.

