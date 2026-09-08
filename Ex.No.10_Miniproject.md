# Ex.No: 10 — Implementation of 2D/3D Game

**DATE:04.09.2026**
**REGISTER NUMBER:212225230006** 

## AIM

To develop a **3D Platformer Game with an AI Enemy** in Unity using **Reinforcement Learning and Unity ML-Agents**.

## ALGORITHM

1. Create a new 3D project in Unity.
2. Design the game environment using platforms, ramps and obstacles.
3. Create the player and enemy characters using 3D GameObjects.
4. Add Rigidbody and Collider components for physics and collision detection.
5. Implement player movement and jumping using C#.
6. Add Unity ML-Agents components to the enemy.
7. Provide the enemy with observations such as its position, player position and velocity.
8. Define continuous actions for enemy movement in the X and Z directions.
9. Assign rewards when the enemy successfully catches the player and penalties when it falls.
10. Train the enemy using the **PPO (Proximal Policy Optimization)** reinforcement-learning algorithm.
11. Set the trained model to inference mode and test the AI enemy in the game.
12. Add a goal zone and display the victory message when the player reaches the goal.
13. Run the game and verify the player, AI enemy and goal functionality.

## PROGRAM

### PlayerMovement.cs

```csharp
using UnityEngine;

[RequireComponent(typeof(Rigidbody))]
public class PlayerMovement : MonoBehaviour
{
    public float moveSpeed = 5f;
    public float jumpForce = 7f;

    private Rigidbody rb;
    private bool isGrounded;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    void Update()
    {
        float x = Input.GetAxis("Horizontal");
        float z = Input.GetAxis("Vertical");

        Vector3 movement =
            new Vector3(x, 0f, z).normalized * moveSpeed;

        rb.velocity = new Vector3(
            movement.x,
            rb.velocity.y,
            movement.z
        );

        if (Input.GetButtonDown("Jump") && isGrounded)
        {
            rb.AddForce(
                Vector3.up * jumpForce,
                ForceMode.Impulse
            );

            isGrounded = false;
        }
    }

    private void OnCollisionStay(Collision collision)
    {
        if (collision.gameObject.CompareTag("Ground"))
            isGrounded = true;
    }
}
```

### EnemyAgent.cs

```csharp
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;

public class EnemyAgent : Agent
{
    public Transform player;
    public float forceMultiplier = 8f;
    public float catchDistance = 1.2f;

    private Rigidbody rb;

    public override void Initialize()
    {
        rb = GetComponent<Rigidbody>();
    }

    public override void OnEpisodeBegin()
    {
        rb.velocity = Vector3.zero;

        transform.localPosition = new Vector3(
            Random.Range(-4f, 4f),
            0.5f,
            Random.Range(-4f, 4f)
        );
    }

    public override void CollectObservations(
        VectorSensor sensor)
    {
        sensor.AddObservation(transform.localPosition);
        sensor.AddObservation(player.localPosition);
        sensor.AddObservation(rb.velocity.x);
        sensor.AddObservation(rb.velocity.z);
    }

    public override void OnActionReceived(
        ActionBuffers actions)
    {
        float x = actions.ContinuousActions[0];
        float z = actions.ContinuousActions[1];

        Vector3 movement = new Vector3(x, 0f, z);

        rb.AddForce(
            movement * forceMultiplier,
            ForceMode.Force
        );

        float distance = Vector3.Distance(
            transform.localPosition,
            player.localPosition
        );

        if (distance < catchDistance)
        {
            SetReward(1.0f);
            EndEpisode();
        }

        if (transform.localPosition.y < -1f)
        {
            SetReward(-1.0f);
            EndEpisode();
        }
    }

    public override void Heuristic(
        in ActionBuffers actionsOut)
    {
        var actions = actionsOut.ContinuousActions;

        actions[0] = Input.GetAxis("Horizontal");
        actions[1] = Input.GetAxis("Vertical");
    }
}
```

## OUTPUT

<img width="470" height="291" alt="Screenshot 2026-09-08 151755" src="https://github.com/user-attachments/assets/c11323de-a442-4db7-a127-f3fd23db1c93" />
<img width="471" height="300" alt="Screenshot 2026-09-08 151743" src="https://github.com/user-attachments/assets/6d08c505-cd2b-4063-b1d8-a4af79e6cb8c" />


## RESULT

Thus, the **3D Platformer Game** was successfully developed using **Unity** and adopted **Reinforcement Learning-based AI technology using Unity ML-Agents and PPO**.
