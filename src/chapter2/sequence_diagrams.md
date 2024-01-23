### Sequence diagrams

We made some sequence diagrams for different (standardized) parts of our system:
- for grabbing parts
- for placing parts
- for screwing parts
- for part stock checking

#### Grabbing parts

Grabbing parts is often the same in this proces, hence why it has a standardized flow.

``` plantuml

@startuml 
control     PLC             as plc
entity      Robotarm        as rob
collections "Parts Stock"   as prt

plc -> prt : Stock check
prt -> prt : Stock check
prt -> plc : Stock ok
plc -> rob : Move gripper above Parts Stock
rob -> plc : Response move
plc -> rob : Open gripper
rob -> plc : Response move
plc -> rob : Move above part
rob -> plc : Response move
plc -> rob : Close gripper
rob -> plc : Response move
plc -> rob : Move above Parts Stock
rob -> plc : Response move

@enduml

```

#### Placing parts

Often, also the mvoements for placing parts can be standardized.

``` plantuml
@startuml
control     PLC             as plc
entity      Robotarm        as rob
collections "Assembly Station"   as ass

plc -> rob : Move gripper above Assembly Station
rob -> plc : Response move
plc -> rob : Move close above Assembly Station
rob -> plc : Response move
plc -> rob : Open gripper
rob -> plc : Response move
plc -> rob : Move above Assembly Station
rob -> plc : Response move

@enduml

```

#### Screwing parts

Screwing parts is a bit harder, but if it happens, the routine of movements is the same.

``` plantuml

@startuml
control     PLC             as plc
entity      Robotarm        as rob
collections "Assembly Table"   as ass

plc -> rob : Move gripper above Assembly Station vertically
rob -> plc : Response move
plc -> rob : Move gripper next to part on Station
rob -> plc : Response move
loop 3 times
    plc -> rob : Close gripper
    rob -> plc : Response move
    plc -> rob : Rotate +180 degrees
    rob -> plc : Response move
    plc -> rob : Open gripper
    rob -> plc : Response move
    plc -> rob : Rotate -180 degrees
    rob -> plc : Response move
end
plc -> rob : Move gripper above Assembly Station vertically
rob -> plc : Response move

@enduml

```

#### Parts stock checking
Later in the project, we decided on using one sensor on the gripper to check if the magazine contains parts, instead of a sensor per magazine. This is cheaper, but require a bit more time for the robot to finish its program cycle. In a project in the real world, a sensor per magazine (position) is the better option if the process is time limited.

``` plantuml

@startuml
control     PLC             as plc
actor       Operator        as ops
entity      Robotarm        as rob
collections "Parts Stock"     as prt

plc -> rob : Move gripper above Parts Stock
rob -> plc : Response move
plc -> prt : Do you have parts?
prt -> prt : Stock check

alt yes stock case

prt -> plc : Yes
plc -> rob : Move to pick location
rob -> plc : Response move
else no stock case
    prt -> plc : No
    plc -> ops : No stock
    ops -> ops : Grab more parts
    ops -> prt : Fill stock
    prt -> plc : Stock is filled
    plc -> ops : Stock is filled confirmed, continue program?
    ops -> plc : Continue
    plc -> rob : Move to pick location
    rob -> plc : Response move

end

@enduml

```