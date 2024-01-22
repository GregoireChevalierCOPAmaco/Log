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