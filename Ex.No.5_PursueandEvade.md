# Ex.No: 5  Implementation of Steering behaviour-Pursue and Evade in Unity
### DATE:                                                                            
### REGISTER NUMBER : 212222240094
### AIM: 
To write a program to simulate the process of Pursue and Evade behavior in Unity using NavigationMeshAgent. 
### Algorithm:
```
1. Create a New Unity Project by Open the  Unity Hub and create a new 3D Project.
2. Name the project "SteeringBehaviors" and select a location. Click Create.
3.Open Unity Scene (default is SampleScene).
  In the Hierarchy, create a Plane:
  Right-click → 3D Object → Plane (this will be the ground).
  Set its Scale to (10, 1, 10) for a larger surface.
  Create three Capsule for the Player, Pursuer, and Evader:
  Rename them to "Player", "Pursuer", and "Evader".
  Set their Y Position to 0.5 (so they sit on the ground).
  Change their Material for better distinction (optional).
3. Add NavMesh and Bake
   Window → AI → Navigation (opens the Navigation tab).
   Select the Plane, go to the Navigation tab, and mark it as Navigation Static.
   Go to the Bake tab and click Bake.
   or
   Add navMeshSurface to plane and bake 
4. Add NavMeshAgent Component
    Select Pursuer, and Evader.
    Click Add Component → Search for NavMeshAgent and add it.
    Adjust NavMeshAgent Settings:
    Player: Set Speed = 5.
    Pursuer: Set Speed = 4.
    Evader: Set Speed = 6.
5. Write a script for  Player_movement behavior and save it

using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class Player_movement : MonoBehaviour
{
    // Start is called before the first frame update
    public float speed;
    void Start()
    {
        float xdir = Input.GetAxis("horizontal") * speed;
        float zdir = Input.GetAxis("vertical") * speed;
        transform.position=new Vector3(xdir,zdir);
    }

    // Update is called once per frame
    void Update()
    {
        
    }
}
**Evader script**
public class Evader : MonoBehaviour
{
    // Start is called before the first frame update
    public NavMeshAgent agent;
    public Transform target;
    public float evadespeed;
    void Start()
    {
        agent= GetComponent<NavMeshAgent>();
    }

    void evade()
    {
        Vector3 fleedir = transform.position - target.position;
        Vector3 evadeposition = transform.position + fleedir.normalized * evadespeed;
        agent.SetDestination(evadeposition);

    }
    // Update is called once per frame
    void Update()
    {
        evade();          
     }
}
**Pursuer script**
public class Pursuer: MonoBehaviour
{
    // Start is called before the first frame update
    NavMeshAgent agent;
    public Transform target;
    public float speed;
    void Start()
    {
        agent=this.GetComponent<NavMeshAgent>();
    }
       // Update is called once per frame
    void pursue()
    {
       Vector3 targetvelocity=target.position-transform.position;
       Vector3 futurepos = transform.position + targetvelocity.normalized*speed;
       agent.SetDestination(target.position);
    } 
    // Update is called once per frame
    void Update()
    {
        pursue();          
     }
}
7. Attach the Script to each player,pursuer and Evader.
   Drag & Drop the Target from the Hierarchy into the "Target" field in the script component ( For pursuer and Evader).
12. Run the game 
13. Stop the program
    
```
### Output:

![image](https://github.com/user-attachments/assets/d12bc108-27da-493a-897e-869670b13128)

![image](https://github.com/user-attachments/assets/7486aadd-c6a9-4579-a4ac-da532c372e0a)








### Result:
Thus the simple pursue and evade behavior was implemented successfully.
