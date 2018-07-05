# ClingoMazeRunner
Clingo program to get to the end of the maze




node(1..9).
step(0..9).
holds(edge(1,2),0).
holds(edge(2,3),0).
holds(edge(2,4),0).
-holds(edge(4,5),0).
holds(edge(5,6),0).
-holds(edge(5,7),0).
holds(edge(7,8),0).
holds(edge(7,9),0).
holds(edge(X,Y),0) :- holds(edge(Y,X),0).

 
%                                 (Sword)         
%                                   6      8(Dead-End)
%                                  /       _
%                                 /        |
%                    (-Door)      /         |
% 1 —  —  — 2 —  —  — 4 —  —  — 5  —  —  — 7 —  —  — 9(Exit)
%           |                            (-Boss)
%           |
%           |
%           3(Key)


%not listed is not holds
-holds(edge(X,Y),0) :- not holds(edge(X,Y),0), node(X), node(Y).
-holds(edge(Y,X),0) :- not holds(edge(Y,X),0), node(X), node(Y).
  
#show occurs/2.

%On node 1 at time 0, not on any other nodes at time 0, no items
holds(on(1),0).
-holds(on(X),0) :- not holds(on(X),0), node(X).
-holds(keypickup).
-holds(swordpickup).


fluent(on(X)) :- node(X).
fluent(edge(X,Y)) :- node(X), node(Y).
fluent(keypickup).
fluent(swordpickup).





action(move(X,Y)):- node(X), node(Y).


holds(on(Y),T+1):- occurs(move(X,Y),T), node(X), node(Y), step(T), step(T+1).
-holds(on(X),T+1):- occurs(move(X,Y),T), node(X), node(Y),step(T), step(T+1).
-occurs(move(X,Y),T) :- -holds(on(X),T), node(X), node(Y), step(T).
-occurs(move(X,Y),T) :-  -holds(edge(X,Y),T), node(X), node(Y), step(T).
%holds(on(3),T) :- holds(keypickup), step(T).
%holds(on(6),T) :- holds(swordpickup), step(T).


holds(edge(4,5),T+1) :- holds(on(3),T), step(T),step(T+1).
holds(edge(5,7),T+1) :- holds(on(6),T), step(T),step(T+1).
holds(keypickup,T+1) :- holds(on(3),T), step(T),step(T+1).
holds(swordpickup,T+1) :- holds(on(6),T), step(T),step(T+1).



sub_goal(on(9)).
%sub_goal(on(6)).
%sub_goal(on(9)).



-goal(T) :- not holds(X,T), sub_goal(X), step(T).
goal(T) :- not -goal(T), step(T).

goal :- goal(T), step(T).
:- not goal.


1{occurs(A,T):action(A)}1 :- not goal(T), step(T).



holds(F,T+1) :- holds(F,T), not -holds(F,T+1), fluent(F), step(T), step(T+1).
-holds(F,T+1) :- -holds(F,T), not holds(F,T+1), fluent(F), step(T), step(T+1).
