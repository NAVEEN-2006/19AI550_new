# Ex.No: 10  Implementation of 2D/3D game of Angry Birds game
### DATE: 25-05-2025                                                                           
### REGISTER NUMBER : 212223040129
### AIM: 
To develop a game a 2D Angry Bird Game in Unity 
### Algorithm:
```
1.Projectile Motion
  Uses gravity and velocity equations to simulate bird flight.
  Handled by the physics engine (like Unity's 2D Rigidbody).
2.Collision Detection & Response
   Detects hits between birds, blocks, and pigs.
   Uses physics algorithms like AABB or SAT (via Box2D/Unity Physics).
3.Damage & Destruction System
   Calculates force of impact and breaks objects if a threshold is crossed.
   Example: high impact destroys wooden blocks.
4.Input-to-Force Mapping
   Converts mouse/touch drag into launch direction and power.
   Force = drag vector × strength multiplier.
5.Basic AI (optional)
   Simple pig reactions or automated level testing bots.
   In advanced versions, reinforcement learning or MCTS used for bots.
6.Level Design / Procedural Generation
   Levels are manually designed or algorithmically generated.
   Some games use pattern-based procedural placement.
```  
### Program:
Birds.cs
```
using UnityEngine;
using System.Collections;
using Assets.Scripts;

[RequireComponent(typeof(Rigidbody2D))]
public class Bird : MonoBehaviour
{

    // Use this for initialization
    void Start()
    {
        //trailrenderer is not visible until we throw the bird
        GetComponent<TrailRenderer>().enabled = false;
        GetComponent<TrailRenderer>().sortingLayerName = "Foreground";
        //no gravity at first
        GetComponent<Rigidbody2D>().isKinematic = true;
        //make the collider bigger to allow for easy touching
        GetComponent<CircleCollider2D>().radius = Constants.BirdColliderRadiusBig;
        State = BirdState.BeforeThrown;
    }



    void FixedUpdate()
    {
        //if we've thrown the bird
        //and its speed is very small
        if (State == BirdState.Thrown &&
            GetComponent<Rigidbody2D>().linearVelocity.sqrMagnitude <= Constants.MinVelocity)
        {
            //destroy the bird after 2 seconds
            StartCoroutine(DestroyAfter(2));
        }
    }

    public void OnThrow()
    {
        //play the sound
        GetComponent<AudioSource>().Play();
        //show the trail renderer
        GetComponent<TrailRenderer>().enabled = true;
        //allow for gravity forces
        GetComponent<Rigidbody2D>().isKinematic = false;
        //make the collider normal size
        GetComponent<CircleCollider2D>().radius = Constants.BirdColliderRadiusNormal;
        State = BirdState.Thrown;
    }

    IEnumerator DestroyAfter(float seconds)
    {
        yield return new WaitForSeconds(seconds);
        Destroy(gameObject);
    }

    public BirdState State
    {
        get;
        private set;
    }
}

```

Brick.cs
```
using UnityEngine;
using System.Collections;

public class Brick : MonoBehaviour
{


    void OnCollisionEnter2D(Collision2D col)
    {
        if (col.gameObject.GetComponent<Rigidbody2D>() == null) return;

        float damage = col.gameObject.GetComponent<Rigidbody2D>().linearVelocity.magnitude * 10;
        //don't play audio for small damages
        if (damage >= 10)
            GetComponent<AudioSource>().Play();
        //decrease health according to magnitude of the object that hit us
        Health -= damage;
        //if health is 0, destroy the block
        if (Health <= 0) Destroy(this.gameObject);
    }

    public float Health = 70f;


    //wood sound found in 
    //https://www.freesound.org/people/Srehpog/sounds/31623/
}

```

Pig.cs
```
using UnityEngine;
using System.Collections;

public class Pig : MonoBehaviour
{

    public float Health = 150f;
    public Sprite SpriteShownWhenHurt;
    private float ChangeSpriteHealth;
    // Use this for initialization
    void Start()
    {
        ChangeSpriteHealth = Health - 30f;
    }

    void OnCollisionEnter2D(Collision2D col)
    {
        if (col.gameObject.GetComponent<Rigidbody2D>() == null) return;

        //if we are hit by a bird
        if (col.gameObject.tag == "Bird")
        {
            GetComponent<AudioSource>().Play();
            Destroy(gameObject);
        }
        else //we're hit by something else
        {
            //calculate the damage via the hit object velocity
            float damage = col.gameObject.GetComponent<Rigidbody2D>().linearVelocity.magnitude * 10;
            Health -= damage;
            //don't play sound for small damage
            if (damage >= 10)
                GetComponent<AudioSource>().Play();

            if (Health < ChangeSpriteHealth)
            {//change the shown sprite
                GetComponent<SpriteRenderer>().sprite = SpriteShownWhenHurt;
            }
            if (Health <= 0) Destroy(this.gameObject);
        }
    }

    //sound found in
    //https://www.freesound.org/people/yottasounds/sounds/176731/
}

```

Slingshot.cs
```
using UnityEngine;
using System.Collections;

public class Pig : MonoBehaviour
{

    public float Health = 150f;
    public Sprite SpriteShownWhenHurt;
    private float ChangeSpriteHealth;
    // Use this for initialization
    void Start()
    {
        ChangeSpriteHealth = Health - 30f;
    }

    void OnCollisionEnter2D(Collision2D col)
    {
        if (col.gameObject.GetComponent<Rigidbody2D>() == null) return;

        //if we are hit by a bird
        if (col.gameObject.tag == "Bird")
        {
            GetComponent<AudioSource>().Play();
            Destroy(gameObject);
        }
        else //we're hit by something else
        {
            //calculate the damage via the hit object velocity
            float damage = col.gameObject.GetComponent<Rigidbody2D>().linearVelocity.magnitude * 10;
            Health -= damage;
            //don't play sound for small damage
            if (damage >= 10)
                GetComponent<AudioSource>().Play();

            if (Health < ChangeSpriteHealth)
            {//change the shown sprite
                GetComponent<SpriteRenderer>().sprite = SpriteShownWhenHurt;
            }
            if (Health <= 0) Destroy(this.gameObject);
        }
    }

}

```
### Output:

![{907D0E37-7D01-4245-8F2F-51357F62F33D}](https://github.com/user-attachments/assets/5b05fc6b-101a-47be-a0ef-73518999a8e8)

![{D075FD3F-6CC7-41C7-9452-5392678F8127}](https://github.com/user-attachments/assets/d0a7cdbd-ae40-4f1c-b389-4640241d932a)


### Result:
Thus the game was developed using Unity and adopted Unity Physics (Rigidbody, Colliders) AI technology.
