# Ex.No: 4  Create a player Movement Script in unity 
### DATE:                                                                            
### REGISTER NUMBER : 212222240094
### AIM: 
To write a program to create a player movement in unity.
### Algorithm:
1. Create a New Unity Project by Open the  Unity Hub and create a new 3D Project,Name the project (e.g., PlayerMovement).
2. Create the Moving Object
   In the Hierarchy, right-click → 3D Object → Capsule (or Sphere).
   Rename it to Player 
4. Adding the Player Movement Behavior Script
   Create the Script-In the Project Window, go to the Assets folder.
   Right-click → Create → C# Script.
5. Write a script for player behavior and save it
6. Attach the Script
   Select player in the Hierarchy - Drag & Drop the playerBehavior script onto the Inspector Panel.
7. Run the game 
8. Stop the program
    
### Program:
```
using UnityEngine;

public class Player_movement : MonoBehaviour
{
    // Start is called once before the first execution of Update after the MonoBehaviour is created
    public float speed=4f;
    void Start()
    {
        
    }

    // Update is called once per frame
    void Update()
    {
        float xdir = Input.GetAxis("Horizontal")*speed;
        float zdir = Input.GetAxis("Vertical")*speed;
        transform.position+=new Vector3(xdir,zdir);
    }
}


```
### Output:
![image](https://github.com/user-attachments/assets/5a626c91-c4f3-48c6-a7bb-189ed3c38fcd)








### Result:
Thus the simple movement behavior was implemented successfully.
