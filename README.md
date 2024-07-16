using UnityEngine;
using UnityEngine.SceneManagement;

public class LevelManager : MonoBehaviour
{
    public int currentLevel = 1;
    public string[] levelSceneNames = new string[] { "Level1", "Level2", "Level3", "Level4", "Level5", "Level6" };
    public float levelTransitionTime = 2.0f;

    private AsyncOperation asyncOperation;

    void Start()
    {
        LoadLevel(currentLevel);
    }

    public void LoadNextLevel()
    {
        currentLevel++;
        LoadLevel(currentLevel);
    }

    public void LoadLevel(int level)
    {
        asyncOperation = SceneManager.LoadSceneAsync(levelSceneNames[level - 1]);
        asyncOperation.allowSceneActivation = true;
    }

    void Update()
    {
        if (asyncOperation!= null && asyncOperation.isDone)
        {
            asyncOperation = null;
        }
    }
}using UnityEngine;

public class PlayerController : MonoBehaviour
{
    public float movementSpeed = 5.0f;
    public float rotationSpeed = 2.0f;
    public float jumpForce = 5.0f;

    private Rigidbody rb;
    private bool isGrounded = true;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    void Update()
    {
        float horizontalInput = Input.GetAxis("Horizontal");
        float verticalInput = Input.GetAxis("Vertical");

        Vector3 movement = new Vector3(horizontalInput, 0, verticalInput);

        rb.AddForce(movement * movementSpeed, ForceMode.VelocityChange);

        float rotation = Input.GetAxis("Mouse X");
        transform.Rotate(0, rotation * rotationSpeed, 0);

        if (Input.GetButtonDown("Jump") && isGrounded)
        {
            rb.AddForce(Vector3.up * jumpForce, ForceMode.Impulse);
            isGrounded = false;
        }
    }

    void OnCollisionEnter(Collision collision)
    {
        if (collision.gameObject.CompareTag("Ground"))
        {
            isGrounded = true;
        }
    }
}using UnityEngine;
using UnityEngine.AI;

public class EnemyAI : MonoBehaviour
{
    public float patrolSpeed = 2.0f;
    public float chaseSpeed = 5.0f;
    public float attackRange = 5.0f;

    private Transform player;
    private NavMeshAgent agent;
    private bool isChasing = false;

    void Start()
    {
        player = GameObject.FindGameObjectWithTag("Player").transform;
        agent = GetComponent<NavMeshAgent>();
    }

    void Update()
    {
        float distanceToPlayer = Vector3.Distance(transform.position, player.position);

        if (distanceToPlayer < attackRange)
        {
            // Attack the player
            AttackPlayer();
        }
        else if (distanceToPlayer < chaseSpeed)
        {
            // Chase the player
            isChasing = true;
            agent.SetDestination(player.position);
        }
        else
        {
            // Patrol the environment
            isChasing = false;
            agent.SetDestination(GetRandomPatrolPoint());
        }
    }

    void AttackPlayer()
    {
        // Play attack animation and deal damage to the player
    }

    Vector3 GetRandomPatrolPoint()
    {
        // Return a random point within
        
