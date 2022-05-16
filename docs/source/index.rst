Shepherd's docs - Reinforcement Learning Agent as a Service
===========================================================

**Shepherd** is a web application using the Django Python web development framework allowing non-experts in Reinforcement Learning (RL) to easily use state-of-the-art RL techniques. It acts as a bridge between web clients, that connect to it over the network (using JSON commands sent over HTTP), and Reinforcement Learning agents available in the Stable-Baselines3. No specific dependencies must be installed on the client's side, nor must a specific programming language be used. Shepherd presents itself to the RL algorithms as a fully standard OpenAIGym environment. At the core of Shepherd, a Transfer Learning method is used to allow RL agents (all under one Shepherd agent), each trained by a different client of Shepherd, to advise and help improve each other. This is why Shepherd is comparable to a single agent, multiple executions setting, without the need to fundamentally modify the RL algorithms from Stable-Baselines3.

We describe below the components of Shepherd, namely: i) the database allowing to keep track of users/clients, and of their RL agents; and ii) the communication workflow between client and server, letting a client's environment to sporadically send observations to an RL agent on the server side, and getting actions in return.

In the following sections, by the term "user", we mean a person that possesses an environment applicable to the use of an RL algorithm. For a user environment to be able to benefit from our service, the only requirements are  that observations can be extracted from the environment, and that the environment can take actions as input. The only contribution left from the user is to implement a client application, establishing the connection with our server, sending observations and receiving actions. However, this part is relatively trivial to implement, and we provide an example.



.. note::

    To use Shepherd, you do not need to know how RL works, however, you do need to have a problem applicable for Reinforcement Learning.


User Guide
----------

.. toctree::

   agent
   algorithm
   example
   what_is_an_environment





