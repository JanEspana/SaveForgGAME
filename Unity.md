# Unity

## Scripts

<!-- -->
### CameraMovementScript

#### Start

Aqui s'estableix la posició inicial de la càmera en les coordenades '(0, 0, -10)'. 'transform.position' accedeix a la posició de l'objecte al que està adjunt a l'script (la càmera en aquest cas).

> void Start()
> {
>   transform.position = new Vector3(0, 0, -10);
> }

#### Update

Aquí es on es gestiona la lògica de moviment de la càmera.

> void Update()
>     {
>        Vector3 playerPosition = GameObject.> FindGameObjectsWithTag("Player")[0].transform.position;
>
>        if (playerPosition.y > 0 )
>        {
>            transform.position = new Vector3(transform.position.x, playerPosition.y, transform.position.z);
>        }
>        if (playerPosition.x > 8)
>        {
>            transform.position = new Vector3(15, transform.position.y, transform.position.z);
>        }
>        else
>        {
>            transform.position = new Vector3(0, transform.position.y, transform.position.z);
>        }
>    }

##### Obtenir la posició del jugador

Troba tots els objectes en la escena amb la etiqueta "Player" i pren la posició del primer objecte trobat.

> Vector3 playerPosition = GameObject.FindGameObjectsWithTag("Player")[0].transform.position;

##### Moure la càmera a l'eix Y

Si la posició Y del jugador es major que 0, la càmera es mou a la mateixa altura que el jugador a l'eix Y, mantenint les seves posicions en els eixos X i Z.

> if (playerPosition.y > 0 )
> {
>     transform.position = new Vector3(transform.position.x, playerPosition.y, transform.position.z);
> }

##### Moure la càmera a l'eix X

Si la posició X del jugador és major que 8, la càmera es mou a la coordenada X 15, mantenint les posicions actuals en els eixos Y i Z.

> if (playerPosition.x > 8)
> {
>     transform.position = new Vector3(15, transform.position.y, transform.position.z);
> }
> else
> {
>     transform.position = new Vector3(0, transform.position.y, transform.position.z);
> }


<!-- -->
### HojaTemporalScript

#### Variables 

**'onQuitarHoja'**: Event Unity que s'activa al treure la fulla.
**'LeafIdle'** i **'LeafFall'**: Sprites per l'estat actiu i desactivat de la fulla.
**'spriteRenderer'**: Referència al component SpriteRenderer del GameObject.
**'collider2D'**: Referència al component Collider2D del GameObject.

> public UnityEvent OnQuitarHoja;
> public Sprite LeafIdle;
> public Sprite LeafFall;
> 
> private SpriteRenderer spriteRenderer;
> private Collider2D collider2D;

#### Start

Obté les referències al SpriteRenderer i al Collider2D del GamObject al que està adjunt aquest script.

> void Start()
> {
>     spriteRenderer = GetComponent<SpriteRenderer>();
>     collider2D = GetComponent<Collider2D>();
> }

#### Update

Canvia l'sprite de la fulla segons si el collider està actiu o no.

> void Update()
> {
>     // Cambia el sprite de la hoja según si el collider está activo o no
>     if (GetComponent<Collider2D>().enabled)
>     {
>         spriteRenderer.sprite = LeafIdle;
>     }
>     else
>     {
>         spriteRenderer.sprite = LeafFall;
>     }
> }

#### ActivarCollider / DesactivarCollider

Aquestes funcions s'utilitzen per desactivar o activar el collider de la fulla.

> void DesactivarCollider()
> {
>     collider2D.enabled = false;
> }
> 
> void ActivarCollider()
> {
>     collider2D.enabled = true;
> }

#### DesactivarHoja

Aquesta funció es crida per desactivar temporalment la fulla. Desactiva el collider després de 1 segon i el reactiva després de 3 segons utilitzant el mètode 'Invoke()'.

> public void DesactivarHoja()
> {
>     Invoke("DesactivarCollider", 1); // Desactiva el collider después de 1 segundo
>     Invoke("ActivarCollider", 3);   // Reactiva el collider después de 3 segundos
> }

<!-- -->
### KingFrogController

#### Variables

**'Rigidbody2D rb;'**: Aquesta variable emmagatzema una referència al component Rigidbody2D adjunt al GameObject.
**'Animator myAnim';'**: Aquesta variable fa referència al component Animator adjunt al GameObject.
**'public float speed;'**: Velocitat del personatge.
**'bool isGrounded;'**: Indica si el personatge està al terra.
**'bool doubleJumpAvailable = false;'**: Indica si el doble salt està disponible.
**'public bool FacingRight = true;'**: Indica si el personatge està mirant a la dreta.
**'int bounceCounter = 0;'**: Contador de rebots.
**'public float maxSpeed;'**: Velocitat màxima del personatge.
**'public int numFlies = 0'**: Numero de mosques recol·lectades.
**'bool isOnHoney = false;'**: Indica si el personatge està sobre mel.
**'public UnityEvent onQuitarHoja;'**: Event de Unity que s'invoca quan es treu una fulla.
**'public UnityEvent OnCaerDeHoja;'**: Event de Unity que s'invoca quan el personatge cau d'una fulla.
**'static int DoubleJumpTrigger = Animator.StringToHash("DoubleJump");'**: Un identificador estàtic que s'utilitza per emmagatzemar el has del trigger "DoubleJump" en el Animator, això es fa per evitar la sobrecàrrega de StringToHash() en temps d'execució.

> Rigidbody2D rb;
> Animator myAnim;
> public float speed;
> bool isGrounded;
> bool doubleJumpAvailable = false;
> public bool FacingRight = true;
> int bounceCounter = 0;
> public float maxSpeed;
> public int numFlies = 0;
> bool isOnHoney = false;
> 
> public UnityEvent OnQuitarHoja;
> public UnityEvent OnCaerDeHoja;
> 
> static int DoubleJumpTrigger = Animator.StringToHash("DoubleJump");

#### Update

Rep un valor entre -1 i 1 que representa la direcció del moviment horitzontal (dreta positiu, esquerra negatiu).

> float move = Input.GetAxis("Horizontal");

En aquest bloc de codi es gestiona la lògica de moviment del personatge quan està en el terra ('isGrounded = true') o en la mel ('isOnHoney = true'). S'inclouen les accions de saltar, moure's horitzontalment i controlar la animació del personatge. 

> if (isGrounded || isOnHoney)
>     {
>         myAnim.SetBool("IsGrounded", true);
>         myAnim.SetBool("IsJumping", false);
> 
>         bounceCounter = 0;
> 
>         if (Input.GetKey(KeyCode.Space))
>         {
>             rb.velocity = new Vector2(rb.velocity.x, 0);
>             rb.AddForce(new Vector2(0, 7f), ForceMode2D.Impulse);
>             isGrounded = false;
>             isOnHoney = false; // Deja de estar en la miel al saltar
>             doubleJumpAvailable = true;
>         }
> 
>         if (Input.GetKey(KeyCode.A))
>         {
>             transform.position += new Vector3(-0.005f, 0, 0);
>             FacingRight = false;
>         }
> 
>         if (Input.GetKey(KeyCode.D))
>         {
>             transform.position += new Vector3(0.005f, 0, 0);
>             FacingRight = true;
>         }
> 
>         myAnim.SetFloat("MoveSpeed", Mathf.Abs(move));
> 
>         if (Mathf.Abs(move) < 0.01f)
>         {
>             myAnim.SetBool("IsRunning", false);
>         }
>         else
>         {
>             myAnim.SetBool("IsRunning", true);
>         }
>     }

En aquest altre bloc es gestiona la lògica de moviment del personatge quna està a l'aire, el que inclou el doble salt. Aqui es on va la lògica que permet al jugador realitzar el doble salt i controlar la animació corresponent.

> else
>     {
>         myAnim.SetBool("IsGrounded", false);
>         myAnim.SetBool("IsJumping", true);
> 
>         if (doubleJumpAvailable)
>         {
>             bounceCounter = 0;
>             if (Input.GetKeyDown(KeyCode.Space) && Input.GetKey(KeyCode.A))
>             {
>                 myAnim.SetTrigger(DoubleJumpTrigger);
>                 StartCoroutine(ReturnToNormalJumpAnimation());
>                 rb.velocity = new Vector2(rb.velocity.x, 0);
>                 rb.velocity = new Vector2(0, rb.velocity.y);
>                 rb.AddForce(new Vector2(-2.5f, 6.5f), ForceMode2D.Impulse);
>                 FacingRight = false;
>                 doubleJumpAvailable = false;
>             }
>             if (Input.GetKeyDown(KeyCode.Space) && Input.GetKey(KeyCode.D))
>             {
>                 myAnim.SetTrigger(DoubleJumpTrigger);
>                 StartCoroutine(ReturnToNormalJumpAnimation());
>                 rb.velocity = new Vector2(rb.velocity.x, 0);
>                 rb.velocity = new Vector2(0, rb.velocity.y);
>                 rb.AddForce(new Vector2(2.5f, 6.5f), ForceMode2D.Impulse);
>                 FacingRight = true;
>                 doubleJumpAvailable = false;
>             }
>         }
>     }

#### Flip

El mètode Flip(), fa que es giri la direcció del personatge segons la seva orientació.Això assegura que l'sprite del personatge estigui mirant en la direcció correcta en tot moment.

> void Flip()
> {
>     if ((FacingRight && transform.localScale.x < 0) || (!FacingRight && transform.localScale.x > 0))
>     {
>         Vector3 newScale = transform.localScale;
>         newScale.x *= -1;
>         transform.localScale = newScale;
>     }
> }

#### Corrutines

Aquest mètode s'utilitza per executar una seqüència d'accions durant un període de temps específic. En aquest cas, després de 0.5 segons ('yield return new WaitForSeconds(0.5f)'), es canvia la variable booleana 'IsJumping' de la animació ('myAnim.SetBool("IsJumping", false)') per tornar a a animació de salt normal.

> IEnumerator ReturnToNormalJumpAnimation()
> {
>     yield return new WaitForSeconds(0.5f);
>     myAnim.SetBool("IsJumping", false); // Cambia de nuevo a la animación de salto normal
> }

#### OnCollisionEnter2D

En aquest bloc, es gestiona la col·lisió del GameObject amb altres colliders. En aquest cas, kingFrog quan toca un obstacle el destrueix.

> void OnCollisionEnter2D(Collision2D collision)
> {
>     if (collision.gameObject.CompareTag("Ground") || collision.gameObject.CompareTag("Honey"))
>     {
>         isGrounded = true;
>         isOnHoney = collision.gameObject.CompareTag("Honey");
>         doubleJumpAvailable = false;           
>         myAnim.SetBool("IsGrounded", true);
>         myAnim.SetBool("IsJumping", false);
>     }
>     else if (collision.gameObject.tag == "Obstacle")
>     {
>         Destroy(collision.gameObject);
>     }
>     else if (collision.gameObject.tag == "Bee")
>     {
>         Destroy(collision.gameObject);
>     }
>     else if (collision.gameObject.tag == "Mushroom")
>     {
>         isGrounded = false;
>         // Lógica de movimiento del personaje al tocar un hongo
>     }
>     else if (collision.gameObject.tag == "SideMushroom")
>     {
>         isGrounded = false;
>         // Lógica de movimiento del personaje al tocar un hongo de lado
>     }
>     else if (collision.gameObject.tag == "Fly")
>     {
>         Destroy(collision.gameObject);
>     }
> }

#### OnCollisionExit2D

Aquest mètode es invocat per Unity quan el 'Rigidbody2D' de l'objecte deixa de co·lisionar amb un altre objecte que té un 'Collider2D'. En aquest cas, s'executa quan el personatge deixa de tocar el terra o la mel.

Quan el personatge deixa de tocar el terra o la mel s'estableixen algunes variables relacionades amb l'estat del personatge. 'isGrounded' s'estableix en 'false' i 'isOnHoney' també s'estableix en 'false', el que indica que el personatge ja no està ni en el terra ni en la mel.

> void OnCollisionExit2D(Collision2D collision)
> {
>     if (collision.gameObject.CompareTag("Ground") || collision.gameObject.CompareTag("Honey"))
>     {
>         isGrounded = false;
>         isOnHoney = false;
>         myAnim.SetBool("IsGrounded", false);
>         myAnim.SetBool("IsJumping", true);
>         if (FacingRight)
>         {
>             rb.velocity = new Vector2(0, rb.velocity.y);
>             rb.AddForce(new Vector2(2.5f, 0), ForceMode2D.Impulse);
>             doubleJumpAvailable = true;
>         }
>         else
>         {
>             rb.velocity = new Vector2(0, rb.velocity.y);
>             rb.AddForce(new Vector2(-2.5f, 0), ForceMode2D.Impulse);
>             doubleJumpAvailable = true;
>         }
>     }
> }

<!-- -->
### MovingObstacle

#### Declaració de la classe i variables

> public float speed;
> Vector3 targetPos;
>
> public GameObject ways;
> public Transform[] wayPoints;
> int pointIndex;
> int pointCount;
> int direction = 1;

**speed**: La velocitat a la que l'obstacle es mou entre els punts.
**targetPos**: La posició objectiu a la que s'està movent actualment l'obstacle.
**ways**: Un objecte que conté tots els waypoints com fills.
**wayPoints**: Un array de 'Transform' que emmagatzemarà les posicions dels waypoints.
**pointIndex**: L'índex del waypoint actual fins el qual s'està movent l'obstacle.
**pointCount**: El numero total de waypoints.
**direction**: La direcció en la que l'obstacle s'està movent (1 endavant, -1 enrere).

#### Awake

Aqui s'omple l'array 'waypoints' amb els 'transform' dels fills de 'ways'. Això assegura que tots els punts de referència estiguin emmagatzemats perquè l'obstacle es mogui entre ells.

> private void Awake()
>     {
>         wayPoints = new Transform[ways.transform.childCount];
>         for (int i = 0; i < ways.gameObject.transform.childCount; i++)
>         {
>             wayPoints[i] = ways.transform.GetChild(i).gameObject.transform;
>         }
>     }

#### Start

Aqui s'inialitza 'pointCount' amb el numero total de waypoints i 'pointIndex' amb l'índex del primer punt objectiu. 'targetPos' s'estableix en la posició del primer waypoint.

> private void Start()
>     {
>         pointCount = wayPoints.Length;
>         pointIndex = 1;
>         targetPos = wayPoints[pointIndex].transform.position;
>     }

#### Update

Es calcula 'step' com la distància que l'obstacle ha de moure's en aquest framse (velocitat multiplicada per el temps transcorregut des de l'últim frame). 'transform.position' s'actualitza per moure l'obstacle fins a 'targetPos'. Si l'obstacle ha arribat a 'targetPos', es crida al mètode 'NextPoint' per determinar el següent punt objectiu.

> private void Update()
>     {
>         var step = speed * Time.deltaTime;
>         transform.position = Vector3.MoveTowards(transform.position, targetPos, step);
> 
>         if (transform.position == targetPos)
>         {
>             NextPoint();
>         }
>     }

#### NextPoint

Aquest mètode s'encarrega d'actualitzar 'pointIndex'0 per apuntar al següent waypoint. Si l'obstacle ha arribat a l'últim waypoint, 'direction' s'inverteix perquè l'obstacle es mogui enrere. Si l'obstacle ha arribat al primer waypoint, 'direction' s'inverteix perquè l'obstacle es mogui endavant. 'targetPos' s'actualitza a la posició del nou waypoint i es crida a 'FlipSpriteVertically' per rotar l'sprite de l'obstacle.

> void NextPoint()
>     {
>         if (pointIndex == pointCount - 1)
>         {
>             direction = -1;
>         }
> 
>         if (pointIndex == 0)
>         {
>             direction = 1;
>         }
> 
>         pointIndex += direction;
>         targetPos = wayPoints[pointIndex].transform.position;
> 
>         FlipSpriteVertically();
>     }

#### FlipSpriteVertically

Inverteix l'eix X de la escala de l'sprite de l'obstacle. Aqixò fa que aquest doni la volta verticalment cada vegada que cambia de direcció.

> void FlipSpriteVertically()
>     {
>         Vector3 scale = transform.localScale;
>         scale.x *= -1;
>         transform.localScale = scale;
>     }


<!-- -->
### PlayerController

#### Declaració de la classe y variables

Es defineixen les variables necessàries per controlar el moviment, estat i animacions del personatge. S'utilitzen 'UnityEvent' per gestionar events específics com treure i recollir la llengua.

> Rigidbody2D rb;
> Animator myAnim;
> public float speed;
> bool isGrounded;
> bool doubleJumpAvailable = false;
> public bool FacingRight = true;
> int bounceCounter = 0;
> public float maxSpeed;
> public int numFlies = 0;
> bool TongueOut = false;
> bool isOnHoney = false;
> bool isDamaged = false;

> public UnityEvent OnQuitarHoja;
> public UnityEvent OnCaerDeHoja;
> public UnityEvent OnSacarLenguaRight;
> public UnityEvent OnSacarLenguaLeft;
> public UnityEvent OnRecogerLengua;

> static int DoubleJumpTrigger = Animator.StringToHash("DoubleJump");

> public float tongueAnimationDuration = 1.0f;

#### Start

Inicialitza les referències a 'Animator' i a 'Rigidbody2D'.

> void Start()
> {
>     myAnim = GetComponent<Animator>();
>     rb = GetComponent<Rigidbody2D>();
> }

#### Update

##### Moviment del personatge en el terra

**'move'**: Emmagatzema l'entrada de l'eix horitzontal (moviment del jugador).
**'if (isGrounded || isOnHoney)'**: S'executa només si el personatge està en el terra o en la mel.
**'Salto'**: Permet al personatge saltar si no està en la mel, es presiona la tecla Espai i la llengua no està treta.
**'Movimiento horizontal'**: Permet al personatge moure's a la esquerra o a la dreta.
**'Sacar la lengua'**: Invoca events i repodueix animacions al treure la llengua.
**'Cambiar la animación'**: Ajusta la animació del personatge sgons el seu moviment horitzontal.

> float move = Input.GetAxis("Horizontal");
> 
> if (isGrounded || isOnHoney)
> {
>     // Establece la animación de estar en el suelo
>     myAnim.SetBool("IsGrounded", true);
>     myAnim.SetBool("IsJumping", false);
> 
>     // Resetea el contador de rebotes si está en el suelo
>     bounceCounter = 0;
> 
>     // Manejo del salto si no está en miel y se presiona la tecla de salto y no se está sacando la lengua
>     if (!isOnHoney && Input.GetKey(KeyCode.Space) && !TongueOut)
>     {
>         rb.velocity = new Vector2(rb.velocity.x, 0);
>         rb.AddForce(new Vector2(0, 7f), ForceMode2D.Impulse);
>         isGrounded = false;
>         doubleJumpAvailable = true;
>         myAnim.SetBool("IsGrounded", false);
>         myAnim.SetBool("IsJumping", true);
>     }
> 
>     // Movimiento horizontal
>     if (Input.GetKey(KeyCode.A))
>     {
>         transform.position += new Vector3(-0.005f, 0, 0);
>         FacingRight = false;
>     }
> 
>     if (Input.GetKey(KeyCode.D))
>     {
>         transform.position += new Vector3(0.005f, 0, 0);
>         FacingRight = true;
>     }
> 
>     // Sacar la lengua
>    if (Input.GetKeyDown(KeyCode.Q) && !TongueOut)
>    {
>         if (FacingRight)
>         {
>             OnSacarLenguaRight.Invoke();
>             isGrounded = false;
>             rb.velocity = new Vector2(0, 0);
>             myAnim.Play("PlayerTongue");
>         }
>         else
>         {
>             OnSacarLenguaLeft.Invoke();
>             isGrounded = false;
>             rb.velocity = new Vector2(0, 0);
>             myAnim.Play("PlayerTongue");
>         }
>         TongueOut = true;
> 
>         StartCoroutine(ReturnToNormalAnimationAfterDelay(tongueAnimationDuration));
>     }
>  
>     // Cambiar la animación si no se está moviendo
>     myAnim.SetFloat("MoveSpeed", Mathf.Abs(move));
> 
>     if (Mathf.Abs(move) < 0.01f)
>     {
>         myAnim.SetBool("IsRunning", false);
>     }
>     else
>     {
>         myAnim.SetBool("IsRunning", true);
>     }
> }

##### Salts dobles

Si el personatge no està en el terra i no està en la mel, estableix la animació de salt.

> if (!isGrounded && !isOnHoney)
> {
>     myAnim.SetBool("IsJumping", true);
> }
> else
> {
>     myAnim.SetBool("IsJumping", false);
> }

Si el personatge no està en el terra ni en la mel, s'executa la lògica per al doble salt:

Es reinicia el contador de rebots
Si es presiona la tecla Espai amb la tecla de Direcció corresponent, es realitza unh doble salt en aquella direcció.
S'estableix l'animació de doble salt.
S'actualitzen les forces aplicades al personatge per realitzar el salt.
S'estableix la direcció del personatge després del salt.
Es desactiva la possibilitat de realitzar un segon doble salt fins que el personatge torni a tocar el terra.

> else
> {
>     myAnim.SetBool("IsGrounded", false);
>     myAnim.SetBool("IsJumping", true);
> 
>     if (doubleJumpAvailable)
>     {
>         bounceCounter = 0;
>         if (Input.GetKeyDown(KeyCode.Space) && Input.GetKey(KeyCode.A))
>         {
>             myAnim.SetTrigger(DoubleJumpTrigger);
>             StartCoroutine(ReturnToNormalJumpAnimation());
> 
>             rb.velocity = new Vector2(rb.velocity.x, 0);
>             rb.velocity = new Vector2(0, rb.velocity.y);
>             rb.AddForce(new Vector2(-2.5f, 6.5f), ForceMode2D.Impulse);
>             FacingRight = false;
>             doubleJumpAvailable = false;
>         }
>         if (Input.GetKeyDown(KeyCode.Space) && Input.GetKey(KeyCode.D))
>         {
>             myAnim.SetTrigger(DoubleJumpTrigger);
>             StartCoroutine(ReturnToNormalJumpAnimation());
> 
>             rb.velocity = new Vector2(rb.velocity.x, 0);
>             rb.velocity = new Vector2(0, rb.velocity.y);
>             rb.AddForce(new Vector2(2.5f, 6.5f), ForceMode2D.Impulse);
>             FacingRight = true;
>             doubleJumpAvailable = false;
>         }
>     }
> }

##### Animació de dany

Si el personatge està danyat, reprodueix l'animació de dany i després restableix l'estat de dany.

> if (isDamaged)
> {
>     myAnim.Play("FrogDamage");
>     isDamaged = false;
> }

#### Corrutines

Restableix l'animació a "PlayerIdle" després de un retràs i canvia l'estat de 'TongueOut' a 'false'.

> IEnumerator ReturnToNormalAnimationAfterDelay(float delay)
> {
>     yield return new WaitForSeconds(delay);
>     myAnim.Play("PlayerIdle");
>     TongueOut = false;
> }

Restableix l'animació de salt després d'un retràs.

> IEnumerator ReturnToNormalJumpAnimation()
> {
>     yield return new WaitForSeconds(0.5f);
>     myAnim.SetBool("IsJumping", false);
> }

#### Flip

Inverteix l'escala del personatge a l'eix X per reflexar la direcció en la que està mirant ('FacingRight').

> void Flip()
> {
>     if ((FacingRight && transform.localScale.x < 0) || (!FacingRight && transform.localScale.x > 0))
>     {
>         Vector3 newScale = transform.localScale;
>         newScale.x *= -1;
>         transform.localScale = newScale;
>     }
> }

#### RecogerLengua i TongueOutFalse

Invoca 'TongueOutFalse' després de 1 segon i estableix 'isGrounded' a 'true' si el doble salt no està disponible.

> public void RecogerLengua()
> {
>     Invoke("TongueOutFalse", 1f);
>     if (!doubleJumpAvailable)
>     {
>         isGrounded = true;
>     }
> }

Cambia l'estat 'TongueOut' a 'false'.

> void TongueOutFalse()
> {
>     TongueOut = false;
> }

#### TocarMosca

Incrementa el contador de mosques recol·lectades.

> public void TocarMosca()
> {
>     numFlies++;
> }

#### OnCollisionEnter2D

Controla les colisions del personatge amb diferents objectes:
**Ground**: Estableix 'isGrounded' a 'true' i desactiva el doble salt.
**Honey**: Estableix 'isOnHoney' a 'true', impedint el salt.
**Obstacle** i **Bee**: Repel·len al personatge, apliquen una força a la direcció oposada i gestionen el rebot.
**Mushroom** i **SideMushroom**: Fan rebotar al personatge a la direcció oposada i activen una animació de rebot en el bolet.

> void OnCollisionEnter2D(Collision2D collision)
> {
>     if (collision.gameObject.tag == "Ground")
>     {
>         isGrounded = true;
>         doubleJumpAvailable = false;
>         myAnim.SetBool("IsGrounded", true);
>         myAnim.SetBool("IsJumping", false);
>     }
>     else if (collision.gameObject.tag == "Honey")
>     {
>         isOnHoney = true;
>         isGrounded = false;
>         doubleJumpAvailable = false;
>     }
>     else if (collision.gameObject.tag == "Obstacle")
>     {
>         isGrounded = false;
>         doubleJumpAvailable = false;
>         isDamaged = true;
>         myAnim.Play("PlayerDamage");
> 
>         if (FacingRight)
>         {
>             rb.velocity = new Vector2(-3.5f, 3f);
>         }
>         else
>         {
>             rb.velocity = new Vector2(3.5f, 3f);
>         }
> 
>         bounceCounter++;
>         if (bounceCounter > 10)
>         {
>             FacingRight = !FacingRight;
>             bounceCounter = 0;
>         }
>     }
>     else if (collision.gameObject.tag == "Bee")
>     {
>         isOnHoney = false;
>         isGrounded = false;
>         doubleJumpAvailable = false;
>         isDamaged = true;
>         myAnim.Play("PlayerDamage");
> 
>         if (FacingRight)
>         {
>             rb.velocity = new Vector2(-5f, 3f);
>         }
>         else
>         {
>             rb.velocity = new Vector2(5f, 3f);
>         }
> 
>         bounceCounter++;
>         if (bounceCounter > 10)
>         {
>             FacingRight = !FacingRight;
>             bounceCounter = 0;
>         }
>     }
>     else if (collision.gameObject.tag == "Mushroom" || collision.gameObject.tag == "SideMushroom")
>     {
>         isGrounded = false;
>         if (FacingRight)
>         {
>             rb.velocity = new Vector2(-2.5f, 6.5f);
>             FacingRight = false;
>         }
>         else
>         {
>             rb.velocity = new Vector2(2.5f, 6.5f);
>             FacingRight = true;
>         }
> 
>         Animator mushroomAnimator = collision.gameObject.GetComponent<Animator>();
>         if (mushroomAnimator != null)
>         {
>            if (collision.gameObject.tag == "Mushroom")
>             {
>                 StartCoroutine(ChangeMushroomAnimation(mushroomAnimator, "MushroomBounce", 0.5f));
>             }
>             else if (collision.gameObject.tag == "SideMushroom")
>             {
>                 StartCoroutine(ChangeMushroomAnimation(mushroomAnimator, "SideMushroomBounce", 0.5f));
>             }
>         }
>     }
> }

#### Corrutina ChangeMushroomAnimation

Reprodueix una animació de rebot en el bolet i després torna a la animació "MushroomIdle" després d'un retràs.

> IEnumerator ChangeMushroomAnimation(Animator animator, string animationName, float delay)
> {
>     animator.Play(animationName);
>     yield return new WaitForSeconds(delay);
>     animator.Play("MushroomIdle");
> }

#### OnCollisionExit2D

Si el personatge surt del terra, s'estableix 'isGrounded' a 'false', es restableix l'estat de les animacions i s'aplica un impuls cap endavant per simular el lliscament.

Si el personatge surt de la mel, s'estableix 'isOnHoney' a 'false' i s'ajusta l'estat de les animacions.

> private void OnCollisionExit2D(Collision2D collision)
> {
>     if (collision.gameObject.tag == "Ground")
>     {
>         isGrounded = false;
>         myAnim.SetBool("IsGrounded", false);
>         myAnim.SetBool("IsJumping", true);
>         
>         // Aplicar impulso hacia adelante para simular el deslizamiento del personaje al dejar el suelo
>         if (FacingRight)
>         {
>             rb.velocity = new Vector2(0, rb.velocity.y);
>             rb.AddForce(new Vector2(2.5f, 0), ForceMode2D.Impulse);
>         }
>         else
>         {
>             rb.velocity = new Vector2(0, rb.velocity.y);
>             rb.AddForce(new Vector2(-2.5f, 0), ForceMode2D.Impulse);
>         }
>         
>         // Restablecer el doble salto si el personaje estaba en el suelo antes de salir
>         if (!doubleJumpAvailable)
>         {
>             doubleJumpAvailable = true;
>         }
>     }
>     
>     if (collision.gameObject.tag == "Honey")
>     {
>         isOnHoney = false;
>         myAnim.SetBool("IsJumping", true);
>     }
> }

#### Mecàniques de les fulles

Quan el personatge està en contacte amb una fulla. Estableix 'isGrounded' a 'true' i permet el doble salt.

> public void IsOnLeaf()
> {
>     isGrounded = true;
>     doubleJumpAvailable = true;
> }

Quan el pesronatge ja no està en contacxte amb una fulla. Estableix 'isGrounded' a 'false' i ajust al'estat de les animacions.

> public void IsNotOnLeaf()
> {
>     isGrounded = false;
>     myAnim.SetBool("IsGrounded", false);
>     myAnim.SetBool("IsJumping", true);
> }

<!-- -->
### RainfrogController

#### Variables

**'rb'**: Referència al component Rigidbody2D de l'objecte.
**'animator'**: Referència al component Animator de l'objecte.
**'speed'**: Velocitat del personatge.
**'isGrounded'**: Indica si el personatge està en el terra.
**'FacingRight'**: Indica si el personatge està mirant a la dreta.
**'bounceCounter'**: Contador de rebots per controlar la direcció després de col·lisionar amb obstacles.
**'maxSpeed'**: Velocitat màxima del personatge.
**'numFlies'**: Número de mosques.
**'isOnHoney'**: Indica si el personatge està en contacte amb la mel.
**'jumpCounter'**: Contador de salts per limitar el numero de salts consecutius.
**'isDamaged'**: Indica si el personatge està danyat.
**'OnQuitarHoja'** i **'OnCaerDeHoja'**: Events Unity que s'activen al treure's i caure d'una fulla.

> Rigidbody2D rb;
> Animator animator;
> public float speed;
> bool isGrounded;
> public bool FacingRight = true;
> int bounceCounter = 0;
> public float maxSpeed;
> public int numFlies = 0;
> bool isOnHoney = false;
> int jumpCounter = 10;
> bool isDamaged = false;
> public UnityEvent OnQuitarHoja;
> public UnityEvent OnCaerDeHoja;

#### Start

S'obtenen les referències al RigidBody i a l'Animator del GamObject al que està adjunt aquest script (RainFrog), i llavors reprodueix una animació d'inici.

> void Start()
> {
>     rb = GetComponent<Rigidbody2D>();
>     animator = GetComponent<Animator>();
>     animator.Play("RainfrogIdle");
> }

#### Update

Aquest codi gestiona el moviment del personatge en funció de les entrades del jugador. Només es permet el moviment si el personatge no està danyat ('isDamaged') i encara té salts disponibles ('jumpCounter > 0').

'bounceCounter = 0;' reinicia el contador de rebots.

Es comprova si es presiona la tecla d'espai i no es presionen A ni D ('Input.GetKeyDown(KeyCode.Space) && !Input.GetKey(KeyCode.A) && !Input.GetKey(KeyCode.D)'), el que desencadena un salt cap amunt.

Si es presiona A i espai simultàniament, el personatge salta cap a l'esquerra.

Si es presiona D i espai simultàniament, el personatge salta cap a la dreta.

Després de cada salt, es redueix en un el contador de salts ('jumpCounter--') i es reprodueix l'animació de salt ('animator.Play("RainfrogJump")').

Flip() s'encarrega de girar la direcció del personatge si es necessari, perquè sempre miri cap a on s'està movent.

> void Update()
> {
>     // Movimiento del personaje si no está dañado y aún tiene saltos disponibles
>     if (!isDamaged && jumpCounter > 0)
>     {
>         bounceCounter = 0; // Reinicia el contador de rebotes
> 
>         // Salto si se presiona la tecla de espacio y no se presiona A ni D
>         if (Input.GetKeyDown(KeyCode.Space) && !Input.GetKey(KeyCode.A) && !Input.GetKey(KeyCode.D))
>         {
>             // Aplica fuerza hacia arriba y desplazamiento horizontal según la situación
>             rb.velocity = new Vector2(0, 0);
>             if (!isOnHoney)
>             {
>                 rb.AddForce(new Vector2(0, 5f), ForceMode2D.Impulse);
>             }
>             else
>             {
>                 rb.AddForce(new Vector2(0, 1f), ForceMode2D.Impulse);
>             }
>             isGrounded = false;
>             jumpCounter--;
>             animator.Play("RainfrogJump"); // Reproduce la animación de salto
>         }
> 
>         // Salto hacia la izquierda si se presiona A y espacio simultáneamente
>         if (Input.GetKey(KeyCode.A) && Input.GetKeyDown(KeyCode.Space))
>         {
>             // Aplica fuerza hacia arriba y desplazamiento a la izquierda
>             rb.velocity = new Vector2(0, 0);
>             if (!isOnHoney)
>             {
>                 rb.AddForce(new Vector2(-2f, 5f), ForceMode2D.Impulse);
>             }
>             else
>             {
>                 rb.AddForce(new Vector2(-2f, 1f), ForceMode2D.Impulse);
>             }
>             FacingRight = false;
>             isGrounded = false;
>             jumpCounter--;
>             animator.Play("RainfrogJump"); // Reproduce la animación de salto
>         }
> 
>         // Salto hacia la derecha si se presiona D y espacio simultáneamente
>         if (!isOnHoney && Input.GetKey(KeyCode.D) && Input.GetKeyDown(KeyCode.Space))
>         {
>             // Aplica fuerza hacia arriba y desplazamiento a la derecha
>             rb.velocity = new Vector2(0, 0);
>             if (!isOnHoney)
>             {
>                 rb.AddForce(new Vector2(2f, 5f), ForceMode2D.Impulse);
>             }
>             else
>             {
>                 rb.AddForce(new Vector2(2f, 1f), ForceMode2D.Impulse);
>             }
>             FacingRight = true;
>             isGrounded = false;
>             jumpCounter--;
>             animator.Play("RainfrogJump"); // Reproduce la animación de salto
>         }
>     }
> 
>     Debug.Log(jumpCounter); // Imprime el contador de saltos en la consola
>     Flip(); // Voltea la dirección del personaje si es necesario
> }

#### OnCollisionEnter2D

Al col·lisionar amb el terra o la mel:

    Si l'objecte amb el que col·lisiona té l'etiqueta "Ground" o "Honey", el personatge es considera en el terra o en la mel ('isGrounded = true;').

    Es reinicia el contador de salts ('jumpCounter = 10;') per permetre un doble salt.

    Es reinicia el contador de rebots ('bounceCounter = 0;').

    S'indica que el personatge no està danyat ('isDamaged = false;').

    Es reprodueix l'animació de Idle ('animator.Play("RainfrogIdle")').

Al col·lisionar amb un obstacle o una abella:

    Si l'objecte té l'etiqueta "Obstacle" o "Bee", el personatge es considera danyat ('isDamaged = true;').

    Es reprodueix l'animació de salt ('animator.Play("RainfrogJump");').

    Depenent de la direcció en la que està mirant el personatge ('FacingRight'), s'aplica una força cap a la direcció oposada per simular un rebot.

    S'incrementa el contador de rebots ('bounceCounter++').

    Si el contador de rebots supera cert límit (10 en aquest cas), es cambia la direcció del personatge ('FacingRight = !FacingRight;') i es reinicia el contador de rebots.

Al col·lisionar amb un bolet:

    Si l'objecte té l'etiqueta "Mushroom" o "SideMushroom", el personatge realitza un salt adicional.

    Depenent de la direcció en la que està mirant el personatge, s'aplica una força cap a la direcció oposada al bolet per simular el salt.

    S'obté la referència al animator del bolet colisionat i es cambia la seva animació utilitzant una corrutina

> void OnCollisionEnter2D(Collision2D collision)
> {
>     // Al tocar el suelo se activa el doble salto
>     if (collision.gameObject.tag == "Ground" || collision.gameObject.tag == "Honey")
>     {
>         isGrounded = true; // El personaje está en el suelo o en miel
>         jumpCounter = 10; // Reinicia el contador de saltos
>         bounceCounter = 0; // Reinicia el contador de rebotes
>         isDamaged = false; // El personaje no está dañado
> 
>         animator.Play("RainfrogIdle"); // Reproduce la animación de idle
>     }
>     // Al chocar con un obstáculo, el personaje se considera dañado
>     else if (collision.gameObject.tag == "Obstacle" || collision.gameObject.tag == "Bee")
>     {
>         isGrounded = false; // El personaje no está en el suelo
>         isDamaged = true; // El personaje está dañado
> 
>         animator.Play("RainfrogJump"); // Reproduce la animación de salto
> 
>         // Manejo del rebote y cambio de dirección después de golpear un obstáculo
>         if (FacingRight)
>         {
>             rb.velocity = new Vector2(0, rb.velocity.y);
>             rb.velocity = new Vector2(rb.velocity.x, 0);
>             rb.AddForce(new Vector2(-3.5f, 2f), ForceMode2D.Impulse);
>             bounceCounter++;
>         }
>         else
>         {
>             rb.velocity = new Vector2(0, rb.velocity.y);
>             rb.velocity = new Vector2(rb.velocity.x, 0);
>             rb.AddForce(new Vector2(3.5f, 2f), ForceMode2D.Impulse);
>             bounceCounter++;
>         }
> 
>         // Si el contador de rebotes supera cierto límite, se cambia la dirección del personaje
>         if (bounceCounter > 10)
>         {
>             FacingRight = !FacingRight;
>             bounceCounter = 0;
>         }
>     }
>     // Al chocar con un hongo, el personaje realiza un salto adicional
>     else if (collision.gameObject.tag == "Mushroom" || collision.gameObject.tag == "SideMushroom")
>     {
>         isGrounded = false; // El personaje no está en el suelo
> 
>         // Salto hacia arriba y hacia la dirección opuesta al hongo
>         if (FacingRight)
>         {
>             rb.velocity = new Vector2(0, rb.velocity.y);
>             rb.velocity = new Vector2(rb.velocity.x, 0);
>             rb.AddForce(new Vector2(-2.5f, 6.5f), ForceMode2D.Impulse);
>             FacingRight = false;
>         }
>         else
>         {
>             rb.velocity = new Vector2(0, rb.velocity.y);
>             rb.velocity = new Vector2(rb.velocity.x, 0);
>             rb.AddForce(new Vector2(2.5f, 6.5f), ForceMode2D.Impulse);
>             FacingRight = true;
>         }
> 
>         // Cambiar la animación del hongo colisionado
>         Animator mushroomAnimator = collision.gameObject.GetComponent<Animator>();
>         if (mushroomAnimator != null)
>         {
>             StartCoroutine(ChangeMushroomAnimation(mushroomAnimator));
>         }
>     }
> }

#### OnCollisionExit2D

'if (collision.gameObject.tag == "Ground")':

    Si l'objecte amb el que el personatge estava en contacte té l'etiqueta "Ground", significa que el personatge ja no està en contacte amb el terra. Per tant, s'estableix 'isGrounded' en 'false', indicant que el personatge no està en el terra.

'if (collision.gameObject.tag == "Honey")':

    Si l'object amb el que el personatge estava en contacte té la etiqueta "Honey", significa que el personatge ja no està en contacte amb la mel. Per tant, s'estableix 'isOnHoney' en 'false', indicant que el personatge ja no està en contacte amb la mel.

> private void OnCollisionExit2D(Collision2D collision)
> {
>     if (collision.gameObject.tag == "Ground")
>     {
>         isGrounded = false; // El personaje ya no está en contacto con el suelo
>     }
>     if (collision.gameObject.tag == "Honey")
>     {
>         isOnHoney = false; // El personaje ya no está en contacto con la miel
>     }
> }

#### Corrutines

Aquestes funcions IEnumerator canvien les animacions dels bolets en el joc. S'utilitzen per crear una pausa en l'execució i després cambiar l'animació al cap d'un cert temps.

> IEnumerator ChangeMushroomAnimation(Animator mushroomAnimator)
> IEnumerator ChangeSideMushroomAnimation(Animator sideMushroomAnimator)

#### Mecàniques del terra i de les Fulles

Es criden aquestes funcions quan el personatge està en una fulla o surt d'aquesta, i ajusten l'estat de 'isGrounded' en conseqüència.

> public void IsOnLeaf()
> public void IsNotOnLeaf()


<!-- -->
### Timer

#### Variables

> public float timeRemaining = 900; // Tiempo inicial en segundos (900 segundos = 15 minutos)
> public bool timerIsRunning = false; // Indica si el temporizador está en funcionamiento

#### Start

En aquest fragment s'inicialitzen les variables 'timeRemaining' i 'timerIsRunning'. 'timeRemaining' representa el temps restant en segons, i 'timerIsRunning' indica si el temporitzador està en funcionament. En el mètode 'Start' s'inicia automàticament el temporitzador i s'estableix el temps inicial en 900 segons (15 minuts).

> private void Start()
> {
> // Inicia el temporizador automáticamente al comenzar
> timerIsRunning = true;
> timeRemaining = 900; // Se establece el tiempo inicial en 15 minutos (900 segundos)
> }

#### Update

S'actualitza el temporitzador si 'timerIsRunning' es 'true'. Si 'timeRemaining' és major que 0, es resta el temps transcorregut des de l'últim fotograma('Time.deltaTime') per actualitzar el temps restant. Si 'timeRemaining' arriba a 0, s'atura el temporitzador i es mostra un missatge a la consola.

> void Update()
> {
>     if (timerIsRunning)
>     {
>         if (timeRemaining > 0)
>         {
>             timeRemaining -= Time.deltaTime; // Resta el tiempo transcurrido desde el último fotograma
>             DisplayTime(timeRemaining); // Muestra el tiempo restante en la pantalla
>         }
>         else
>         {
>             Debug.Log("Time has run out!");
>             timeRemaining = 0;
>             timerIsRunning = false; // Detiene el temporizador cuando el tiempo se agota
>         }
>     }
> }

#### DisplayTime

Aquest mètode pren el temps restant com a paràmetre i el mostra en format de minuts i segons en un objecte de text anomenat "Timer". Primer, es suma 1 a 'timeToDisplay' per evitar que el temporitzador arribi a 0 abans d'actualitzar el text. Després, es calculen els minuts i segons restants. Finalment, es busca l'objecte "Timer" en la escena i s'actualitza el seu text per mostrar el temps restant e format de minuts i segons.

> void DisplayTime(float timeToDisplay)
> {
>     timeToDisplay += 1; // Se suma 1 para evitar que el temporizador llegue a 0 antes de cambiar el texto
> 
>     float minutes = Mathf.FloorToInt(timeToDisplay / 60); // Calcula los minutos
>     float seconds = Mathf.FloorToInt(timeToDisplay % 60); // Calcula los segundos
> 
>     // Busca el objeto "Timer" en la escena
>     GameObject timer = GameObject.Find("Timer");
>     // Cambia el texto del objeto "Timer" para mostrar el tiempo restante formateado en minutos y segundos
>     timer.GetComponent<TextMeshProUGUI>().text = string.Format("{0:00}:{1:00}", minutes, seconds);
> }

<!-- -->
### TongueScript

#### Variables

> public UnityEvent OnSacarLengua; // Evento que se activa al sacar la lengua
> public UnityEvent OnRecogerLengua; // Evento que se activa al recoger la lengua
> public UnityEvent OnTocarMosca; // Evento que se activa al tocar una mosca

#### Start

> void Start()
> {
>     // El objeto comienza oculto al inicio del juego
>     gameObject.SetActive(false);
> }

#### SacarLenguaRight i SacarLenguaLeft

Aquests mètodes són cridats per treure la llengua del personatge cap a la dreta o cap a l'esquerra. S'ajusta l'escala de la llengua perquè apunti en la direcció corresponent. Després, s'activa l'objecte llengua, es troba la posició del jugador i es coloca la llengua aprop del jugador. Finalment s'invoca el mètode 'RecogerLengua' després de un petit retràs per simular el moviment de la llengua.

> public void SacarLenguaRight()
> {
>     transform.localScale = new Vector3(-1, 1, 1); // Escala la lengua para que mire hacia la derecha
>     gameObject.SetActive(true); // Activa el objeto lengua
>     GameObject player = GameObject.Find("Frog"); // Encuentra el objeto "Frog" (el jugador)
>     transform.position = new Vector3(player.transform.position.x + 0.8f, player.transform.position.y - 0.45f, 0); // Coloca la lengua cerca del jugador
>     Invoke("RecogerLengua", 0.1f); // Invoca el método para recoger la lengua después de un pequeño retraso
> }
> 
> public void SacarLenguaLeft()
> {
>     transform.localScale = new Vector3(1, 1, 1); // Escala la lengua para que mire hacia la izquierda
>     gameObject.SetActive(true); // Activa el objeto lengua
>     GameObject player = GameObject.Find("Frog"); // Encuentra el objeto "Frog" (el jugador)
>     transform.position = new Vector3(player.transform.position.x - 0.8f, player.transform.position.y - 0.45f, 0); // Coloca la lengua cerca del jugador
>     Invoke("RecogerLengua", 0.1f); // Invoca el método para recoger la lengua después de un pequeño retraso
> }

#### RecogerLengua

Aquest mètode es crida per recollir la llengua. Desactiva l'objecte llengua i activa l'event 'OnRecogerLengua', que pot ser escoltat per altres components per realitzar accions adicionals.

> public void RecogerLengua()
> {
>     gameObject.SetActive(false); // Desactiva el objeto lengua
>     OnRecogerLengua.Invoke(); // Activa el evento de recoger la lengua
> }

#### OnTriggerEnter2D

Aquest mètode es crida quan el collider de la llengua entra en contacte amb un altre collider. Si l'objecte amb el que colisiona té l'etiqueta "Fly", es destrueix aquell objecte i s'activa l'event 'OnTocarMosca'. Si l'objecte té l'etiqueta "Bee", simplement es destrueix sense activar cap event.

> public void OnTriggerEnter2D(Collider2D collision)
> {
>     if (collision.gameObject.tag == "Fly")
>     {
>         Destroy(collision.gameObject); // Destruye la mosca
>         OnTocarMosca.Invoke(); // Activa el evento de tocar una mosca
>     }
>     if (collision.gameObject.tag == "Bee")
>     {
>         Destroy(collision.gameObject); // Destruye la abeja
>     }
> }

<!-- -->
### Nuts

#### Variables

> public float fallSpeed = 10.0f; // Velocidad de caída de la bellota
> private Vector3 _startingPos; // Posición inicial de la bellota
> private Animator _animator; // Referencia al componente Animator
> private bool _isFalling = true; // Flag que indica si la bellota está cayendo
> private float FallDistance = 25.0f; // Distancia de caída desde la posición inicial
> public GameObject Squirrel; // Referencia al objeto Squirrel

#### Start

En aquest fragment es declaren les variables i s'inicialitzen

> void Start()
> {
>     // Guardar la posición inicial
>     _startingPos = transform.position;
>     // Obtener el componente Animator
>     _animator = GetComponent<Animator>();
> }

#### Update

Si l'aglà està caient, la fa baixar seguint una trajectòria. Si la distància entre la posició inicial i la posició actual de l'aglà és major o igual que la distància de caiguda, s'activa l'animació de trencar-se i s'atura la caiguda.

> void Update()
> {
>     if (_isFalling)
>     {
>         // Hacer que la bellota caiga
>         transform.Translate(Vector3.down * fallSpeed * Time.deltaTime, Space.World);
> 
>         // Si la distancia entre la posición inicial y la posición actual es mayor o igual que la distancia de caída,
>         // activar la animación de rompimiento y detener la caída
>         if (Vector3.Distance(_startingPos, transform.position) >= FallDistance)
>         {
>             _animator.SetTrigger("NutBreak");
>             _isFalling = false;
>         }
>     }
> }

#### OnTriggerEnter

Aquest mètode es crida quan l'aglà entra en colisió amb un altre collider que no sigui ell mateix. Atura la caiguda, oculta l'objecte i activa l'animació "SquirrelThrow" a l'objecte Squirrel si existeix.

> private void OnTriggerEnter(Collider other)
> {
>     // Verificar si el objeto colisionado no es la propia bellota
>     if (other.gameObject != gameObject)
>     {
>         // Detener la caída de la bellota
>         _isFalling = false;
>         // Ocultar la bellota
>         gameObject.SetActive(false);
> 
>         // Si hay un objeto Squirrel y tiene un Animator, activar la animación "SquirrelThrow"
>         if (Squirrel != null)
>         {
>             Animator SquirrelAnimator = Squirrel.GetComponent<Animator>();
>             if (SquirrelAnimator != null)
>             {
>                 SquirrelAnimator.SetTrigger("SquirrelThrow");
>             }
>         }
>     }
> }

#### OnNutBreakAnimationEnd

Aquest mètode es crida quan finalitza l'animació de trencament de l'aglà. Restableix la posició d'aquest a la posició inicial, permet que torni a cauren reprodueix l'animació "NutIdle" i activa l'animació "SquirrelThrow" a l'objecte Squirrel si existeix.

> public void OnNutBreakAnimationEnd()
> {
>     // Resetear la posición de la bellota a la posición inicial
>     transform.position = _startingPos;
>     _isFalling = true; // Permitir que la bellota vuelva a caer
>     _animator.Play("NutIdle"); // Volver a reproducir la animación de la bellota en reposo
> 
>     // Si hay un objeto Squirrel y tiene un Animator, activar la animación "SquirrelThrow"
>     if (Squirrel != null)
>     {
>         Animator SquirrelAnimator = Squirrel.GetComponent<Animator>();
>         if (SquirrelAnimator != null)
>         {
>             SquirrelAnimator.SetTrigger("SquirrelThrow");
>         }
>     }
> }