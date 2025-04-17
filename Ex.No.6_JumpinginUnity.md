# Ex.No: 6  Implementation of Jumping  behaviour- Unity
### DATE:                                                                            
### REGISTER NUMBER : 212222240094
### AIM: 
To write a program to simulate the process of jumping in Unity.
### Algorithm:

1. Create a new 3D Unity project
2. Add a Plane
3. Right-click Hierarchy → 3D Object → Plane → Rename to Ground
4. Add a Cube (Player)
5. Right-click Hierarchy → 3D Object → Cube → Rename to Player
6. Set Position: (0, 0.5, 0)
7. Add a Rigidbody to the Player
8. With the Player selected: Inspector → Add Component → Rigidbody
9. Set Constraints > Freeze Rotation X, Z (optional for stability)
10. Create the Jump Script and Apply the Script Player
11. Run the game
12. Press Play
13. Press Spacebar to jump
14. Your cube should only jump when touching the ground

### Program:
```
using UnityEngine;

public class PlayerJump : MonoBehaviour
{
    private Rigidbody rb;
    public float jumpForce = 5f;
    private bool isGrounded;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
            isGrounded = false;
        }
    }

    private void OnCollisionEnter(Collision collision)
    {
        if (collision.gameObject.CompareTag("Ground"))
        {
            isGrounded = true;
        }
    }
}
```
### Output:
Before Jumping:

![image](https://github.com/user-attachments/assets/ef3a5e62-6339-4fa1-b801-8b0e900b51fb)

Jumping:

![image](https://github.com/user-attachments/assets/3a231701-694a-4dc3-9d8c-50911ab6b0f2)








### Result:
Thus the simple jumping behavior was implemented successfully.
