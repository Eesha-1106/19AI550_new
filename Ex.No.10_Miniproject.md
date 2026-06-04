# Ex.No: 10  Implementation of 2D/3D game Obstacle Run
### DATE: 04-06-26                                                                        
### REGISTER NUMBER :212224240040 
### AIM: 
To develop a game Obstacle Run in Unity 
### Algorithm:
```
1.A Unity 3D project was created and a ground plane was set up for the endless runner path.
2.A player character was added with automatic forward movement and lane-switching controls.
3.Multiple obstacle prefabs were created and assigned colliders for detection.
4.A spawn system was implemented to generate obstacles randomly in different lanes ahead of the player.
5.Coins were added as collectibles to increase the score on trigger detection.
6.Collision detection was used to end the game when the player hits an obstacle.
7.A simple UI was created to display score and game over screen.
8.The game was tested and adjusted for speed, spawn rate, and difficulty balance.
```  
### Program:
PlayerController.cs
```
using UnityEngine;
public class PlayerController : MonoBehaviour
{
    [Header("Movement")]
    public float forwardSpeed = 5f;
    public float laneDistance = 3f;
    public float laneChangeSpeed = 10f;
    [Header("Jump")]
    public float jumpForce = 15f;
    private Rigidbody rb;
    private int currentLane = 1; // 0 = left, 1 = center, 2 = right
    private bool isGrounded = true;
    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }
    void Update()
    {
        // Auto Run Forward
        transform.Translate(Vector3.forward * forwardSpeed * Time.deltaTime);
        // Move Left
        if (Input.GetKeyDown(KeyCode.A) || Input.GetKeyDown(KeyCode.LeftArrow))
        {
            currentLane--;
            if (currentLane < 0)
                currentLane = 0;
        }
        // Move Right
        if (Input.GetKeyDown(KeyCode.D) || Input.GetKeyDown(KeyCode.RightArrow))
        {
            currentLane++;
            if (currentLane > 3)
                currentLane = 3;
        }
        // Jump
        if (Input.GetKeyDown(KeyCode.Space) && isGrounded)
        {
            Debug.Log("Jump Pressed");
            rb.linearVelocity = new Vector3(
                rb.linearVelocity.x,
                0,
                rb.linearVelocity.z
            );
            rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
            isGrounded = false;
        }
        // Lane Movement
        float targetX = 0f;
        if (currentLane == 0)
            targetX = -laneDistance;
        if (currentLane == 3)
            targetX = laneDistance;
        Vector3 targetPosition = new Vector3(
            targetX,
            transform.position.y,
            transform.position.z
        );
        transform.position = Vector3.Lerp(
            transform.position,
            targetPosition,
            laneChangeSpeed * Time.deltaTime
        );
    }
    void OnCollisionEnter(Collision collision)
{
    if (collision.gameObject.CompareTag("Ground"))
    {
        isGrounded = true;
    }
    if (collision.gameObject.CompareTag("Obstacle"))
    {
        Debug.Log("GAME OVER");
        Time.timeScale = 0f;
    }
}
}
```
GroundSpawner.cs
```
using UnityEngine;
using System.Collections.Generic;
public class GroundSpawner : MonoBehaviour
{
    public GameObject groundPrefab;
    public Transform player;
    private float spawnZ = 0;
    private float tileLength = 30f;
    private int tilesOnScreen = 5;
    private List<GameObject> activeTiles = new List<GameObject>();
    void Start()
    {
        for (int i = 0; i < tilesOnScreen; i++)
        {
            SpawnTile();
        }
    }
    void Update()
    {
        if (player.position.z > spawnZ - (tilesOnScreen * tileLength))
        {
            SpawnTile();
            DeleteTile();
        }
    }
    void SpawnTile()
    {
        GameObject tile = Instantiate(groundPrefab,
            Vector3.forward * spawnZ,
            Quaternion.identity);
        activeTiles.Add(tile);
        spawnZ += tileLength;
    }
    void DeleteTile()
    {
        Destroy(activeTiles[0]);
        activeTiles.RemoveAt(0);
    }
}
```
CameraFlow.cs
```
using UnityEngine;
public class CameraFollow : MonoBehaviour
{
    public Transform player;
    private Vector3 offset;
    void Start()
    {
        offset = transform.position - player.position;
    }
    void LateUpdate()
    {
        transform.position = player.position + offset;
    }
}
```
coin.cs
```
using UnityEngine;
public class Coin : MonoBehaviour
{
    private void OnTriggerEnter(Collider other)
    {
        if (other.CompareTag("Player"))
        {
            ScoreManager.Instance.AddScore(1);
            Destroy(gameObject);
        }
    }
}
```
TileSpawner.cs
```
using UnityEngine;
public class TileSpawner : MonoBehaviour
{
    public GameObject[] obstaclePrefabs;
    public GameObject coinPrefab;
    void Start()
    {
        SpawnObstacles();
        SpawnCoins();
    }
    void SpawnObstacles()
{
    int obstacleCount = Random.Range(1, 4);
    for (int i = 0; i < obstacleCount; i++)
    {
        float[] lanes = { -2f, 0f, 2f };
        float x = lanes[Random.Range(0, lanes.Length)];
        float z = Random.Range(20f, 180f);
        Vector3 pos = transform.position + new Vector3(x, 1f, z);
        GameObject randomObstacle =
            obstaclePrefabs[Random.Range(0, obstaclePrefabs.Length)];
        Instantiate(randomObstacle, pos, Quaternion.identity, transform);
    }
}
    void SpawnCoins()
    {
        int coinCount = Random.Range(5, 12);
        for (int i = 0; i < coinCount; i++)
        {
            float[] lanes = { -3f, 0f, 3f };
            float x = lanes[Random.Range(0, lanes.Length)];
            float z = Random.Range(10f, 190f);
            Vector3 pos = transform.position + new Vector3(x, 1f, z);
            Instantiate(coinPrefab, pos, Quaternion.identity, transform);
        }
    }
}
```


### Output:
<img width="959" height="506" alt="image" src="https://github.com/user-attachments/assets/47c0490e-b9c3-4b21-bea1-1f92792a66e2" />

### Result:
Thus the game was developed using Unity and adopted Procedural Content Generation (PCG) AI technology.
