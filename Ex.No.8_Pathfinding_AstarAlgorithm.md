# Ex.No: 8  Implementation of Path finding using A* algorithm
### DATE:                                                                            
### REGISTER NUMBER : 212222240094
### AIM: 
To write a program to create graph using waypoints and use A* algorithm to find path between source and destination.
### Algorithm:
1. Create a New Unity Project by Open the  Unity Hub and create a new 3D Project,Name the project (e.g., Pathfinding).
2. Create Waypoints in Scene => Create empty or sphere GameObjects ( minimum 4)  and  name it as Waypoint1, Waypoint2, ..., Waypoint4
   Position them freely in the scene (not on a grid)
3. Write a waypoint script to draw the lines between neighbors.
4.Attach waypoint.cs script to each waypoint GameObject and Manually assign neighbors in the Inspector to form a graph.
5. Create a empty game object and name it as WaypointManager - to manage all waypoints
6. Attach Waypoint script to it
7.Write a Pathfinding algorithm using A*search
8. Create a Game Object for Player ( choose capsule or any others) and attach the script to move player from start to end waypoints

### Program:
```
**#1.Waypoint.cs**
using UnityEngine;
using System.Collections.Generic;

public class Waypoint : MonoBehaviour {
    public List<Waypoint> neighbors = new List<Waypoint>();

    // Optional: Draw connections in the editor
    private void OnDrawGizmos() {
        Gizmos.color = Color.yellow;
        foreach (var neighbor in neighbors) {
            if (neighbor != null) {
                Gizmos.DrawLine(transform.position, neighbor.transform.position);
            }
        }
    }
}
**#2. WaypointGraph.cs**
using UnityEngine;

public class WaypointGraph : MonoBehaviour {
    public Waypoint[] allWaypoints;

    void Awake() {
        allWaypoints = FindObjectsOfType<Waypoint>();
    }
}
**#3.Pathfinding.cs**
using System.Collections.Generic;
using UnityEngine;
public class Pathfinding : MonoBehaviour {
    public static List<Waypoint> FindPath(Waypoint start, Waypoint goal) {
        var openSet = new List<Waypoint>();
        var cameFrom = new Dictionary<Waypoint, Waypoint>();
        var gScore = new Dictionary<Waypoint, float>();
        var fScore = new Dictionary<Waypoint, float>();

        foreach (var wp in FindObjectsOfType<Waypoint>()) {
            gScore[wp] = float.PositiveInfinity;
            fScore[wp] = float.PositiveInfinity;
        }

        gScore[start] = 0f;
        fScore[start] = Vector3.Distance(start.transform.position, goal.transform.position);
        openSet.Add(start);

        while (openSet.Count > 0) {
            Waypoint current = openSet[0];
            foreach (var wp in openSet) {
                if (fScore[wp] < fScore[current]) {
                    current = wp;
                }
            }

            if (current == goal) {
                return ReconstructPath(cameFrom, current);
            }

            openSet.Remove(current);

            foreach (var neighbor in current.neighbors) {
                float tentativeG = gScore[current] + Vector3.Distance(current.transform.position, neighbor.transform.position);
                if (tentativeG < gScore[neighbor]) {
                    cameFrom[neighbor] = current;
                    gScore[neighbor] = tentativeG;
                    fScore[neighbor] = tentativeG + Vector3.Distance(neighbor.transform.position, goal.transform.position);

                    if (!openSet.Contains(neighbor)) {
                        openSet.Add(neighbor);
                    }
                }
            }
        }

        return null; // No path found
    }

    private static List<Waypoint> ReconstructPath(Dictionary<Waypoint, Waypoint> cameFrom, Waypoint current) {
        var path = new List<Waypoint> { current };
        while (cameFrom.ContainsKey(current)) {
            current = cameFrom[current];
            path.Insert(0, current);
        }
        return path;
    }
}

**#4.AICharacter.cs**
using UnityEngine;
using System.Collections.Generic;

public class AICharacter : MonoBehaviour {
    public Waypoint startWaypoint;
    public Waypoint goalWaypoint;
    public float speed = 3f;

    private List<Waypoint> path;
    private int currentIndex = 0;

    void Start() {
        path = Pathfinding.FindPath(startWaypoint, goalWaypoint);
    }

    void Update() {
        if (path == null || currentIndex >= path.Count) return;

        Vector3 target = path[currentIndex].transform.position;
        transform.position = Vector3.MoveTowards(transform.position, target, speed * Time.deltaTime);

        if (Vector3.Distance(transform.position, target) < 0.1f) {
            currentIndex++;
        }
    }
}
Check the following
1. Waypoints placed in scene
2. Neighbors set manually via Inspector
3. WaypointGraph script on a manager
4. AICharacter assigned a start and goal
```
### Output:
#### Before Running the Program:
![image](https://github.com/user-attachments/assets/da318c5c-447f-448a-b1ae-b501394a1ac7)

#### After Running the Program:
![image](https://github.com/user-attachments/assets/f393f988-4e58-4b10-9143-7ad62dba22f2)

### Result:
Thus the pathfinding algorithm was sucessfully implemented.
