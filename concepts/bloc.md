# BLoC - Business Logic Component

BLoC is a design pattern that separates presentation from business logic. It uses streams and reactive aspects of programming extensively.

## Cubit

![image](https://raw.githubusercontent.com/felangel/bloc/master/docs/assets/cubit_architecture_full.png)

The base for Bloc is Cubit. Cubit is a stream that manages any type of state.
- It requires an initial state
- Get state - through state getter
- Mutate state - through emit function

![image](https://raw.githubusercontent.com/felangel/bloc/master/docs/assets/cubit_flow.png)

## Bloc

![image](https://raw.githubusercontent.com/felangel/bloc/master/docs/assets/bloc_architecture_full.png)

Bloc = advanced cubit.
- Relies on events to trigger state changes rather than functions
- Extends cubit
- Maps events to states

![image](https://raw.githubusercontent.com/felangel/bloc/master/docs/assets/bloc_flow.png)

TL;DR takes a stream of events as input and transforms them into a stream of states as output.
