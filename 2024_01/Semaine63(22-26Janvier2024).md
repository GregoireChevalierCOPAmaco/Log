**22 Janvier**
- [ ] Testing exhaustif de corrélation données caisse / données logicielles
    - [ ] Listing des paramètres à tester
        - [ ] Données caisse
            - Date d'installation 
            - Version firmware 
            - État caisse 
            - Temps depuis dernière utilisation ✔
            - Évènements dernière heure 
            - Évènements trappe semaine 
            - Température CPU 
            - Courant moteur 
            - Temps moteur 
            - Temps d'ouverture 
            - Temps de fermeture 
            - Niveau de maintenance 
- [x] Résolution de 502 suite à stack trace : 
    ```
    <--- Last few GCs --->

    [19:0x4dac0e0] 86521949 ms: Mark-sweep (reduce) 1951.0 (1988.4) -> 1949.2 (1985.7) MB, 1700.1 / 0.1 ms  (+ 131.8 ms in 24 steps since start of marking, biggest step 18.4 ms, walltime since start of marking 1864 ms) (average mu = 0.275, current mu = 0.188)[19:0x4dac0e0] 86524193 ms: Mark-sweep (reduce) 1950.2 (1985.7) -> 1948.6 (1985.7) MB, 2241.3 / 0.1 ms  (average mu = 0.144, current mu = 0.001) allocation failure scavenge might not succeed


    <--- JS stacktrace --->

    FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory
    1: 0xb090e0 node::Abort() [node]
    2: 0xa1b70e  [node]
    3: 0xce1a20 v8::Utils::ReportOOMFailure(v8::internal::Isolate*, char const*, bool) [node]
    4: 0xce1dc7 v8::internal::V8::FatalProcessOutOfMemory(v8::internal::Isolate*, char const*, bool) [node]
    5: 0xe99435  [node]
    6: 0xe99f16  [node]
    7: 0xea843e  [node]
    8: 0xea8e80 v8::internal::Heap::CollectGarbage(v8::internal::AllocationSpace, v8::internal::GarbageCollectionReason, v8::GCCallbackFlags) [node]
    9: 0xeabdfe v8::internal::Heap::AllocateRawWithRetryOrFailSlowPath(int, v8::internal::AllocationType, v8::internal::AllocationOrigin, v8::internal::AllocationAlignment) [node]
    10: 0xe6d33a v8::internal::Factory::NewFillerObject(int, bool, v8::internal::AllocationType, v8::internal::AllocationOrigin) [node]
    11: 0x11e61e6 v8::internal::Runtime_AllocateInYoungGeneration(int, unsigned long*, v8::internal::Isolate*) [node]
    12: 0x15d9e59  [node]
    Aborted (core dumped)
    ```
    - [x] Suggestion gpt : 
    ```
    The error message indicates that your Node.js process has run out of memory (JavaScript heap out of memory). This often happens when your application is using more memory than is available or allowed by the V8 JavaScript engine.

    Here are some steps you can take to address this issue:

    Increase Memory Limit:

    You can try increasing the memory available to Node.js by launching your Node.js process with the --max-old-space-size flag. For example:
    bash
    Copy code
    node --max-old-space-size=4096 your_script.js
    This sets the maximum heap size to 4 GB. Adjust the value based on your system's available memory.
    Optimize Your Code:

    Review your code for memory leaks and optimize it. Look for areas where memory is not being released properly, and ensure that you are not unintentionally holding references to objects.
    Use a Tool for Memory Profiling:

    Tools like the Chrome Developer Tools or Node.js built-in --inspect flag can help you identify memory issues. You can profile your application and analyze memory usage to find areas for improvement.
    Update Node.js:

    Ensure that you are using a recent version of Node.js. Newer versions may have improvements in memory management.
    Consider Using a Process Manager:

    If your application is running in a production environment, consider using a process manager like PM2. It can help manage processes, including automatic restarts and resource management.
    Check for Memory-Intensive Operations:

    Identify any operations in your code that might be memory-intensive, especially if they involve large data sets or processing. Optimize or paginate such operations if necessary.
    Inspect External Dependencies:

    If your application relies on external dependencies, check if any of them have memory leaks or inefficiencies. Make sure you are using the latest versions of your dependencies.
    ```
    - [x] Quickfix ; restart containers
        - [x] connection à l'instance ```ssh -i "Predict-Beta.pem" ubuntu@ec2-3-76-238-223.eu-central-1.compute.amazonaws.com```
        - [x] Redémarrage des containers
    - [x] Création du ticket KP-695 : Fix le problème de "JavaScript heap out of memory"
        - [x] Modification du docker compose prod, ajout dans kmo-predict-back de : 
        ```
        command: npm run start:prod node --max-old-space-size=4096
        ```
        - [x] Commit
        - [x] Push
        - [x] PR
        - [x] pull develop en prod
        - [x] Recréation des containers front back : 
        ```
        sudo docker stop kmo-predict-front kmo-predict-back
        sudo  docker compose --env-file .env.prod -f docker-compose-prod.yml up --always-recreate-deps --build
        ```
    - [x] Le problème persiste. Un algo doit amasser de la mémoire over time.
    - [ ] Trouver la source du problème
        - [ ] Revert des pr jusqu'à revenir à un état fonctionnel
            - Revert "fix(front): Fix the time since last use display in checkout modal-KP-691)"
            - Revert "feat(front): Add color code for deactivated stores in /cop(KP-687)"
    - [ ] Essai en local ; transposition des données de la base de prod en local pour voir comportement
    Fail des imports
    - [x] suppression des events en base de prod pour voir comment réagit l'application : succès.
- [x] Création du ticket : KP-699 Définir le coût en mémoire de la route d'API /GET kmoBoxes
- [x] Repopulation de la db de dev en local
- [x] Demande à gpt : 
```
...
this is my backend controller
the getKmoBoxesWithEvents method gets all events of all gateways. In my database i have more than 350 000 events, which is consuming way too much memory.

1. How do i monitor the memory usage of a fucntion ?

2. What would you change to optimize the method ?
```
Réponse : 
```
To monitor the memory usage of a function in a Node.js application, you can use tools like process.memoryUsage(). Here's a simple example of how you can use it:

typescript
Copy code
async function getKmoBoxesWithEvents(): Promise<GetLambdaKmoBoxDto[]> {
  try {
    const startMemory = process.memoryUsage();

    // Your existing logic for fetching data

    const endMemory = process.memoryUsage();
    console.log('Memory usage (start):', startMemory);
    console.log('Memory usage (end):', endMemory);

    const lambdaBox: GetLambdaKmoBoxDto[] = kmoBoxes.map((item) => ({
      ...item,
      gatewayMac: item.gateway.mac,
    }));
    return lambdaBox;
  } catch (error) {
    // Handle errors
    console.error('Error in getKmoBoxesWithEvents:', error);
    throw new HttpException('Failed to get KmoBoxes with events', 500);
  }
}
As for optimizing the getKmoBoxesWithEvents method, fetching a large number of events from the database can be resource-intensive. Here are some suggestions to optimize the method:

Pagination: Instead of fetching all events at once, consider implementing pagination. Fetch a limited number of events per request, and allow the client to request more pages as needed.

Limit the Number of Events: If you don't need all events, consider adding a date range or limiting the number of events fetched based on some criteria.

Database Indexing: Ensure that the database columns used in your queries are properly indexed. Indexing can significantly improve query performance.

Select Only Needed Columns: Instead of using * in your queries, select only the columns you need. This can reduce the amount of data transferred between the database and your application.

Caching: Consider implementing caching mechanisms if the data doesn't change frequently. This can reduce the need to fetch data from the database on every request.

Database Query Optimization: Analyze the generated SQL queries and optimize them. Tools like the TypeORM Query Logger can help you see the actual SQL queries being executed.

Batch Processing: If possible, process data in smaller batches instead of loading all the data into memory at once.

Remember that these optimizations depend on the specific requirements of your application, and it's essential to test and measure the impact of each optimization to ensure it meets your performance goals.
```
- [ ] Monitoring des ressources du controller kmo boxes



**23 Janvier**
- [ ] Monitoring des ressources du controller kmo boxes
    - [x] Connexion à la prod ```ssh -i "Predict-Beta.pem" ubuntu@ec2-3-76-238-223.eu-central-1.compute.amazonaws.com```
        - [x] Modification du kmoBox service dans le back, ajout de code de monitoring de memory dans la method getKmoBoxesWithEvents: 
        ```
        async getKmoBoxesWithEvents(): Promise<GetLambdaKmoBoxDto[]> {
            try {
            const kmoBoxes = await KmoBox.createQueryBuilder('kmoBox')
                .leftJoinAndSelect('kmoBox.events', 'event')
                .addSelect('kmoBox.gatewayMac')
                .leftJoinAndSelect('kmoBox.gateway', 'gateway')
                .addSelect('gateway.mac')
                .getMany();

            for (const kmoBox of kmoBoxes) {
                if (kmoBox.lastMaintenance != null) {
                const eventsAfterMaintenance = await Event.createQueryBuilder('event')
                    .where('event.kmoBox.mac = :kmoBoxId', { kmoBoxId: kmoBox.mac })
                    .andWhere('event.datetime > :lastMaintenance', {
                    lastMaintenance: kmoBox.lastMaintenance,
                    })
                    .getMany();

                kmoBox.events = eventsAfterMaintenance;
                }
            }

            const lambdaBox: GetLambdaKmoBoxDto[] = kmoBoxes.map((item) => ({
                ...item,
                gatewayMac: item.gateway.mac,
            }));

            return lambdaBox;
            } catch (error) {
            throw new HttpException('Failed to fetch KmoBoxes with events', 500);
            }
        }
        ```
    - [x] Relance des dockers
    - [x] Connexion à predict
    - [x] Allumage
    - [ ] Monitoring
        - [x] Table events vide : 
        ```
         ==================================================
        Memory usage (start): {
        rss: 114167808,
        heapTotal: 57171968,
        heapUsed: 50317968,
        external: 2135207,
        arrayBuffers: 387737
        }
        Memory usage (end): {
        rss: 114249728,
        heapTotal: 57171968,
        heapUsed: 50721888,
        external: 2189564,
        arrayBuffers: 425622
        }
        ```
        puis récurrence de la methode quatre autres fois : 
        ```
        ==================================================
        Memory usage (start): {
          rss: 114429952,
          heapTotal: 57171968,
          heapUsed: 50708648,
          external: 2135207,
          arrayBuffers: 387737
        }
        Memory usage (end): {
          rss: 114905088,
          heapTotal: 57171968,
          heapUsed: 51359040,
        external: 2209590,
          arrayBuffers: 445648
        }
        ==================================================
        Memory usage (start): {
          rss: 114298880,
          heapTotal: 57171968,
          heapUsed: 50425968,
          external: 2135207,
          arrayBuffers: 387737
        }
        Memory usage (end): {
          rss: 114552832,
          heapTotal: 57171968,
          heapUsed: 51116656,
          external: 2229616,
          arrayBuffers: 462794
        }
        ==================================================
        Memory usage (start): {
          rss: 114561024,
          heapTotal: 57171968,
          heapUsed: 50816192,
          external: 2135207,
          arrayBuffers: 387737
        }
        Memory usage (end): {
          rss: 114315264,
          heapTotal: 57171968,
          heapUsed: 50856712,
          external: 2249642,
          arrayBuffers: 481597
        }
        ==================================================
        Memory usage (start): {
          rss: 114298880,
          heapTotal: 57171968,
          heapUsed: 50601064,
          external: 2135207,
          arrayBuffers: 387737
        }
        Memory usage (end): {
          rss: 114970624,
          heapTotal: 57171968,
          heapUsed: 51516728,
          external: 2270549,
          arrayBuffers: 502504
        }
        ```
    - [x] Réimport des 349000 events en base
    - [x] Predict crash avec heap limit
    - [x] Réduction de la table à 19400 events
    - [x] Arrêt des containers
    - [x] Suppression des containers
    - [x] Relance des containers
    - [ ] Benchmark Memory Useage (end)
 | Events  | rss       | heapTotal     | heapUsed    | external  | arrayBuffers
> | 000000 | 114.970.624 | 57.171.968 | 51.516.728 | 2.270.549 | 502.504
| 001000 | 144.396.288 | 70.017.024 | 60.270.312 | 3.344.662 | 2.224.879
| 010000 | 200.359.936 | 125.517.824 | 97.214.704 | 5.482.565 | 4.363.234
| 020000 | 265.805.824 | 186.163.200 | 162.733.384 | 15.235.793 | 14.116.710 
| 050000 | 538.877.952 | 414.986.240 | 375.865.928 | 58.056.636 | 65.937.553
| 100000 | 813.940.736 | 672.014.336 | 636.550.184 | 65.792.727 | 64.673.647
| 150000 | 947.228.672 | 771.317.760 | 536.745.080 | 96.858.320 | 95.739.493
| 200000 | 1.303.130.112 | 1.096.142.848 | 934.523.232 | 128.171.432 | 127.052.605
| 250000 | 1.539.579.904 | 1.340.178.432 | 905.232.184 | 160.733.815 | 159.614.738
| 300000 | memory heap & crash du back
- [ ] Poursuite des recherches sur l'optimisation et le profiling
    - [x] Check de (https://www.emretosun.dev/post/mastering-performance-optimization-in-nestjs-a-comprehensive-guide)
    - [x] Check de (https://ariya.io/2013/07/profile-guided-javascript-optimization)
    - [x] Check de (https://www.atatus.com/blog/nestjs-monitoring-with-atatus/#monitoring-nest.js)
- [ ] Réunion des objectifs 


**25 Janvier**
- [ ] Écriture des tickets KP-705/706/707
- [ ] Passage du fix KP-691 en prod
  - [ ] ```git reset --hard``` & extraction du code de test mémoire :
  ```
  async getKmoBoxesWithEvents(): Promise<GetLambdaKmoBoxDto[]> {
    try {
      const startMemory = process.memoryUsage();
    const kmoBoxes = await KmoBox.createQueryBuilder('kmoBox')
      .leftJoinAndSelect('kmoBox.events', 'event')
      .addSelect('kmoBox.gatewayMac')
      .leftJoinAndSelect('kmoBox.gateway', 'gateway')
      .addSelect('gateway.mac')
      .getMany();

    for (const kmoBox of kmoBoxes) {
      if (kmoBox.lastMaintenance != null) {
        const eventsAfterMaintenance = await Event.createQueryBuilder('event')
          .where('event.kmoBox.mac = :kmoBoxId', { kmoBoxId: kmoBox.mac })
          .andWhere('event.datetime > :lastMaintenance', {
            lastMaintenance: kmoBox.lastMaintenance,
          })
          .getMany();

        kmoBox.events = eventsAfterMaintenance;
      }
    }

    const endMemory = process.memoryUsage();
    console.log('==================================================');

    console.log('Memory usage (start):', startMemory);
    console.log('Memory usage (end):', endMemory);

    const lambdaBox: GetLambdaKmoBoxDto[] = kmoBoxes.map((item) => ({
      ...item,
      gatewayMac: item.gateway.mac,
    }));
    return lambdaBox;
  } catch (error) {
    // Handle errors
    console.error('Error in getKmoBoxesWithEvents:', error);
    throw new HttpException('Failed to get KmoBoxes with events', 500);
  }
  }
  ```
  - [ ] ```git pull origin develop``` & rebuild docker