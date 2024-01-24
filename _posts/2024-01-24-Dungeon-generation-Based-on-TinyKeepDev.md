## Dungeon Generator


### Final Result
![FinalResult](/Images/FinalResult.gif)


### The process of dungeon generation


In year 2, block B of my education at Breda University of Applied Sciences. I wanted to create a dungeon generation, and I followed a very good blog post regarding the process of creating this type of dungeon <a href="#links">[1]</a> there are a couple of libraries used in the process. I used a template that was provided by Breda University of Applied Sciences that included an ECS system, which was a big part of this project. I also used CMT for the delauney triangulation.


### The first step

#### Spawing rooms in an ellipse
![Spawning in Ellipse](/Images/RandomRoomsInEllipse.gif)

The first step in developing this type of generation was creating an ellipse that rooms spawn in. I have a minimum number of rooms the user wants and can tweak in the UI. These are the blue rooms. I spawn various rooms of random sizes, where the blue rooms are bigger than the other rooms. Then, based on these sizes, I give them a weight for the separation.

### Physics

![Physics Seperation](/Images/PhysicsSeperationOfRooms.gif)

I decided to use physics to separate them because it was more interesting to me to use, but it is still doable, for example, with the use of a steering algorithm. I decide how much to move the rooms based on their weight.

After the rooms are moving less than a certain threshold, they stop moving and snap to their closest position on a grid.

### Corridors

#### Triangulation
![Triangulation](/Images/RoomTriangulation.png)

Once they are placed on a grid, I start connecting them up using Dalauney triangulation. I add the centre of each room to the algorithm, and it triangulates all the points.

#### Minimum Span Tree
![Minimum Span Tree](/Images/RoomMinimumSpanTree.png)

After the triangulation, I use Kruskal's minimum spawn tree to remove the extra edges that would cause looping.

#### Readding Edges
![Readding Edges](/Images/RoomReaddingCorridors.png)

Once that was done, I added a certain percentage of the edges back from the original triangulation to make the dungeon layout more interesting.

#### Corridors
![Corridor Connections](/Images/CorridorsConnecting.png)

Once all the connections are setup, I then start creating corridors based on the centre of all the rooms.

#### Corridor rooms
![Corridor Rooms](/Images/CorridorsConnectingChangingRooms.png)

I then use all the rooms that overlap with the corridors and make them a different colour to visualise rooms that can be used as part of a corridor.

### End

Once all the corridors are done being created, the dungeon generator returns a struct holding the entt IDs of the blue rooms, the yellow rooms, and the corridors.

### Links

[1] <a href="https://www.gamedeveloper.com/programming/procedural-dungeon-generation-algorithm">Procedural Dungeon Generation Algorithm</a>

<br>

![BuasLogo](/Images/Logo_BUas_Black.png)

<span class="hljs-comment">#BUas #BUasGames #C++</span>