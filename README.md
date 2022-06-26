### EX NO : 01
### DATE  : 04.04.2022
# <p align="center"> Developing AI Agent with PEAS Description </p>

## AIM

To find the PEAS description for the given AI problem and develop an AI agent.

## THEORY
A vacuum-cleaner world with 6 locations.
Each location can be clean or dirty.
The agent can move left, right, up, down,and can clean the square that it occupies.

## PEAS DESCRIPTION
| Agent type    | performance  measurement      |environment  |   Actuators         |  sensors                       | 
|-------------  | ---------------------------   | ----------- |-------------------- | ------------------------------ | 
| vaccum cleaner| cleanliness ,number of moments| Rooms       |  wheels,suction tool|  location, cleanliness         |


## DESIGN STEPS
### STEP 1:
The inputs are location of the agent and the status of the location

### STEP 2:
The output of the system is Right Left and Suck.

### STEP 3:
Agent Type:Vaccum Cleaner
Performance Measure: Cleanliness , Number of Movements
Environment: Rooms
Actuators: Wheels and Suction tool
Sensor: Location Sensor and Cleanliness sensor

### STEP 4:
The agent should detect the location and suck if the location it is dirty,else it should move to the next location.

### STEP 5:
The performance is measured with the number of movements and the cleaning action of the agent.

## PROGRAM
```
Developed by:Gowri M
Register No :212220230019
```

```python
import random
import time

class Thing:
    """
        This represents any physical object that can appear in an Environment.
    """

    import random

class Thing:
    """
        This represents any physical object that can appear in an Environment.
    """

    def is_alive(self):
        """Things that are 'alive' should return true."""
        return hasattr(self, 'alive') and self.alive

    def show_state(self):
        """Display the agent's internal state. Subclasses should override."""
        print("I don't know how to show_state.")


class Agent(Thing):
    """
        An Agent is a subclass of Thing
    """

    def __init__(self, program=None):
        self.alive = True
        self.performance = 0
        self.program = program

    def can_grab(self, thing):
        """Return True if this agent can grab this thing.
        Override for appropriate subclasses of Agent and Thing."""
        return False

def TableDrivenAgentProgram(table):
    """
    This agent selects an action based on the percept sequence.
    It is practical only for tiny domains.
    To customize it, provide as table a dictionary of all
    {percept_sequence:action} pairs.
    """
    percepts = []

    def program(percept):
        percepts.append(percept)
        action=table.get(tuple(percept))
        return action

    return program

loc_A, loc_B, loc_C, loc_D, loc_E, loc_F = (0,0), (0,1), (0,2), (1,2), (1,1), (1,0) # The two locations for the Vacuum world
#G-20 H-21 I-22
#F-10 E-11 D-12
#A-00 B-01 C-02

def TableDrivenVacuumAgent():
    """
    Tabular approach towards vacuum world
    """
    table = {(loc_A, 'Clean'): 'Right1',
             (loc_A, 'Dirty'): 'Suck',
             (loc_B, 'Clean'): 'Right2',
             (loc_B, 'Dirty'): 'Suck',
             (loc_C, 'Clean'): 'Up',
             (loc_C, 'Dirty'): 'Suck',
             (loc_D, 'Clean'): 'Left1',
             (loc_D, 'Dirty'): 'Suck',
             (loc_E, 'Clean'): 'Left2',
             (loc_E, 'Dirty'): 'Suck',
             (loc_F, 'Clean'): 'Down',
             (loc_F, 'Dirty'): 'Suck',
             
             
    }
    return Agent(TableDrivenAgentProgram(table))
#right1,2,3,4 start left1,2 up1,2

class Environment:
    """Abstract class representing an Environment. 'Real' Environment classes
    inherit from this. Your Environment will typically need to implement:
        percept:           Define the percept that an agent sees.
        execute_action:    Define the effects of executing an action.
                           Also update the agent.performance slot.
    The environment keeps a list of .things and .agents (which is a subset
    of .things). Each agent has a .performance slot, initialized to 0.
    Each thing has a .location slot, even though some environments may not
    need this."""

    def __init__(self):
        self.things = []
        self.agents = []

    def percept(self, agent):
        """Return the percept that the agent sees at this point. (Implement this.)"""
        raise NotImplementedError

    def execute_action(self, agent, action):
        """Change the world to reflect this action. (Implement this.)"""
        raise NotImplementedError

    def default_location(self, thing):
        """Default location to place a new thing with unspecified location."""
        return None

    def is_done(self):
        """By default, we're done when we can't find a live agent."""
        return not any(agent.is_alive() for agent in self.agents)

    def step(self):
        """Run the environment for one time step. If the
        actions and exogenous changes are independent, this method will
        do. If there are interactions between them, you'll need to
        override this method."""
        if not self.is_done():
            actions = []
            for agent in self.agents:
                if agent.alive:
                    actions.append(agent.program(self.percept(agent)))
                else:
                    actions.append("")
            for (agent, action) in zip(self.agents, actions):
                self.execute_action(agent, action)

    def run(self, steps=1000):
        """Run the Environment for given number of time steps."""
        for step in range(steps):
            if self.is_done():
                return
            self.step()

    def add_thing(self, thing, location=None):
        """Add a thing to the environment, setting its location. For
        convenience, if thing is an agent program we make a new agent
        for it. (Shouldn't need to override this.)"""
        if not isinstance(thing, Thing):
            thing = Agent(thing)
        if thing in self.things:
            print("Can't add the same thing twice")
        else:
            thing.location = location if location is not None else self.default_location(thing)
            self.things.append(thing)
            if isinstance(thing, Agent):
                thing.performance = 0
                self.agents.append(thing)

    def delete_thing(self, thing):
        """Remove a thing from the environment."""
        try:
            self.things.remove(thing)
        except ValueError as e:
            print(e)
            print("  in Environment delete_thing")
            print("  Thing to be removed: {} at {}".format(thing, thing.location))
            print("  from list: {}".format([(thing, thing.location) for thing in self.things]))
        if thing in self.agents:
            self.agents.remove(thing)


class TrivialVacuumEnvironment(Environment):
    """This environment has two locations, A and B. Each can be Dirty
    or Clean. The agent perceives its location and the location's
    status. This serves as an example of how to implement a simple
    Environment."""

    def __init__(self):
        super().__init__()
        self.status = {loc_A: random.choice(['Clean', 'Dirty']),
                       loc_B: random.choice(['Clean', 'Dirty']),
                       loc_C: random.choice(['Clean', 'Dirty']),
                       loc_D: random.choice(['Clean', 'Dirty']),
                       loc_E: random.choice(['Clean', 'Dirty']),
                       loc_F: random.choice(['Clean', 'Dirty']),}        

    def thing_classes(self):
        return [ TableDrivenVacuumAgent]

    def percept(self, agent):
        """Returns the agent's location, and the location status (Dirty/Clean)."""
        return agent.location, self.status[agent.location]
    def clean_check(self):
        number_of_clean_rooms=0
        for key in [loc_A, loc_B, loc_C, loc_D, loc_E, loc_F]:
            if self.status[key] =='Clean':
                number_of_clean_rooms=number_of_clean_rooms+1
        return number_of_clean_rooms
    def execute_action(self, agent, action):
        """Change agent's location and/or location's status; track performance.
        Score 10 for each dirt cleaned; -1 for each move."""
        if (self.clean_check()!=9):
            
            if action=='Right1':
                agent.location = loc_B
                agent.performance -=1
            elif action=='Right2':
                agent.location = loc_C
                agent.performance -=1
            elif action=='Left1':
                agent.location = loc_E
                agent.performance -=1
            elif action=='Left2':
                agent.location = loc_F
                agent.performance -=1
            elif action=='Up':
                agent.location = loc_D
                agent.performance -=1
            elif action=='Start':
                agent.location = loc_A
                agent.performance -=1
            elif action=='Suck':
                if self.status[agent.location]=='Dirty':
                    agent.performance+=10
                self.status[agent.location]='Clean'

    def default_location(self, thing):
        """Agents start in either location at random."""
        return random.choice([loc_A, loc_B, loc_C, loc_D, loc_E, loc_F])


if __name__ == "__main__":
    agent = TableDrivenVacuumAgent()
    environment = TrivialVacuumEnvironment()
    environment.add_thing(agent)
    print('Agent Before Action\n\n',environment.status)
    print('\nAgent Location : ',agent.location)
    print('\nAgent Performance : ',agent.performance)
    print("\nClean rooms : ",environment.clean_check())

    environment.run(steps=6)
    print('\n\nAgent after Action\n\n',environment.status)
    print('\nAgent Location : ',agent.location)
    print('\nAgent Performance : ',agent.performance)
    print("\nClean rooms : ",environment.clean_check())
        


```


## OUTPUT
![output](./static/img/aiot.png)
</br>

## RESULT

Thus, an AI agent was developed and PEAS description is given.
