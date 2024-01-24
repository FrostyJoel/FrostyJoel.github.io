## Dungeon Generator

### Final Result
![FinalResult](/Images/FinalResult.gif)

### The process of dungeon generation

In year 2, block B of my education at Breda University of Applied Sciences. I wanted to create a dungeon generation, and I followed a very good blog post regarding the process of creating this type of dungeon. <a href="#links">[1]</a>

There are a couple of libraries used in this project. I used a template that was provided by Breda University of Applied Sciences that included an Entt library <a href="#links">[2]</a>, which was a big part of this project. I also used CDT triangulation <a href="#links">[3]</a> for the Delaunay triangulation.<a href="#links">[4]</a>

### Usage of APIs

#### Creating systems

I first start by creating the systems required for the generation. I created a struct that held the data needed for the dungeon, which is also a system in this case, so it can be used globally.

```cpp
Dungeon::DungeonGeneration2D::DungeonGeneration2D()
{
     auto& dungeonStruct = Engine.ECS().CreateSystem<DungeonStruct>();
     auto& physics = Engine.ECS().CreateSystem<physics::World>(0.02f);
     auto& corridorConnections = Engine.ECS().CreateSystem<CorridorConnections>();
}
```

When creating a dungeon, I use a seed based on the current time. This seed is used for the positioning of the rooms in the ellipse.

```cpp
    const int randomSeed = static_cast<int>(time(nullptr));
    dungeonStruct.SetSeed(randomSeed);
```

After the generation is done, the rooms and corridors can be retrieved via the dungeon struct.

```cpp
    dungeonStruct.GetDungeonRoomsAndCorridors().GetCorridor();
    dungeonStruct.GetDungeonRoomsAndCorridors().GetCorridorRooms();
    dungeonStruct.GetDungeonRoomsAndCorridors().GetRooms();
```

### The first step

#### Spawing rooms in an ellipse
![Spawning in Ellipse](/Images/RandomRoomsInEllipse.gif)

The first step in developing this type of generation was creating an ellipse that rooms spawn in. I have a minimum number of rooms the user wants and can tweak in the UI. These are the blue rooms. I spawn various rooms of random sizes, where the blue rooms are bigger than the grey rooms. The other grey rooms are used as potential connecting corridors once the generation gets to that point.

### Physics

![Physics Seperation](/Images/PhysicsSeperationOfRooms.gif)

While the rooms were being created, I gave them a weight based on their sizes. This is for the physics to see how much I need to move the rooms.

I decided to use a physics system to separate the rooms because it was already implemented in the template and I have more experience using it. But it is still doable with out relling on a physics system. It can be done with a steering system which is what the original creator of this generation used. I decide how much to move the rooms based on their weight.

After the rooms are moving less than a certain threshold, they stop moving and snap to their closest position on a grid.

### Corridors

#### Triangulation
![Triangulation](/Images/RoomTriangulation.png)

Once they are placed on a grid, I start connecting them up using Dalauney triangulation. <a href="#links">[4]</a>

I add the centre of each room to a list of vertices, so it can be triangulated. To triangulate everything, I use the CDT triangulation library. <a href="#links">[3]</a>

After the triangulation, I transfer the points into a graph.

#### Minimum Span Tree
![Minimum Span Tree](/Images/RoomMinimumSpanTree.png)

I then use Kruskal's minimum spawn tree algorithm <a href="#links">[5]</a> on to the graph, remove the edges that would cause looping. It does this by checking the weight of all the edges and sorting them in non-decreasing order. It uses the lowest-weight edges and checks if they are causing looping in the graph. If there is looping, these edges are discarded.

#### Readding Edges
![Readding Edges](/Images/RoomReaddingCorridors.png)

Once the minimum span tree algorithm is done removing all the looping edges, I add a percentage of the edges back from the original triangulation to make the dungeon layout more interesting.

#### Corridors
![Corridor Connections](/Images/CorridorsConnecting.png)

Once all the connections are setup, I then start creating corridors based on the centre of all the rooms. I create a connection using both rooms centers x and y position but I make sure the corridor isn't diagonal.

#### Corridor rooms
![Corridor Rooms](/Images/CorridorsConnectingChangingRooms.png)

I then check if any corridors are overlapping with rooms and if there is an overlap these rooms are marked as a connection room (Yellow). To check these connections I use AABB to find overlap between the corridors and the grey rooms.

### End
Once all the corridors are done being created, the dungeon generator returns a struct holding the entt IDs of the blue rooms, the yellow rooms, and the corridors. This can be used to get the entity components if needed.

```cpp
struct Rooms
{
public:
    void SetRooms(const std::vector<entt::entity>& v) { rooms = v; }
    void SetCorridorRooms(const std::vector<entt::entity>& v) { corridorRooms = v; }
    void SetCorridor(const std::vector<entt::entity>& v) { corridors = v; }

    const std::vector<entt::entity>& GetRooms() const { return rooms; }
    const std::vector<entt::entity>& GetCorridorRooms() const { return corridorRooms; }
    const std::vector<entt::entity>& GetCorridor() const { return corridors; }

private:
    std::vector<entt::entity> rooms;
    std::vector<entt::entity> corridorRooms;
    std::vector<entt::entity> corridors;
};
```

### Links

[1] <a href="https://www.gamedeveloper.com/programming/procedural-dungeon-generation-algorithm">Procedural Dungeon Generation Algorithm</a>

[2] <a href="https://github.com/skypjack/entt">Entt Library</a>

[3] <a href="https://github.com/artem-ogre/CDT">CDT Library</a>

[4] <a href="https://en.wikipedia.org/wiki/Delaunay_triangulation#:~:text=In%20mathematics%20and%20computational%20geometry,any%20triangle%20in%20the%20DT.">Delaunay triangulation</a>

[5] <a href="https://www.geeksforgeeks.org/kruskals-minimum-spanning-tree-algorithm-greedy-algo-2/">Kruskal's minimum span tree</a>

<br>

![BuasLogo](/Images/Logo_BUas_Black.png)

<span class="hljs-comment">#BUas #BUasGames #C++</span>