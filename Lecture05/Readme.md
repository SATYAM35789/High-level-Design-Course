Today's target :- 
 1. Rate Limiting
 2. Caching Strategies

## Rate Limiting 
Structure :
 - Client (req)
 - Server (res)

- We know that client sends the Request to server and Server process the request and send back to the client which is also know as Client-server Architecture. 
- A Client can send the request multiple time. -> And then it is possible that our server get loaded. 
- Problem :- 
  1. A client can send too many request within a timeframe. Due to that our server can got crashed. 
  - DOS/DDOS attack :- Where many computer come together like DS and attack on the specific system means sends the request together on the server. So thousands of request will come to the server due to that sever got overload, and got crashed. 

 2. Server Cost Expensive :- 
   - Different application like swiggy, zomato,etc uses google maps api, to find the exact location , to loacte the current position,etc So if you using the api professionally then server charge it. 
   -> Us case mai har ek request expensive hoti hai, So jab bhi hamari application us api ke server ko request karegi then charge lagega. 
 
 -> Solution of this problem is Rate-Limiting. 

### Definition of the Rate-Limiting :- 
 - Rate limiting, limit the client that wo particular time mai kitna request bhej sakta hai on a server.
 - It is responsibility of the application/server to have rate limiting to prevent the above problem's. 

How  to implement the rate limiting? To implement the rate-limiting we have different algorithum we will be seeing in detail here.

### Where to implement the Rate-Limiting?
 1. Client side
 2. Server Side
 3. Middleware 

 - Client Side :- 
   - When we apply the rate limiting on client side, then whenever any client hit the server with broswer. Then at the client side we have rate limiting so detect the multiple request. 
    - It is very simple and server do not need to handle the logic at the server. 
   - This is poor way to implement because So anything we implement on the client side, we do not have the access of that. And client can manipulate the implementation, see the rate-limit code. 
     - We do not have the control so if want to do some modification in the rate-limiting is problematic.
     - Hacking the rate limit is easy. 

 - Other two middleware and Server side are good to implement the Rate Limiting. 

 - Middleware : We are having the middleware layer also in the application building. So this is layer which exist between the client and server. Now Client sends the request to middleware and then middleware send it to the server and then server process the request and send the response to middleware and then middleware sends that response to the client. 
  - Generally the API gateways is called the middleware. API gateway is middleware where we implement the many logic. 
  - One of the logic is Rate-Limiting. 

### How to choose the place between the Middleware and Server side for rate-limiting? 
- On the basis of applicatiion use case we decide which should be used between them. 
1. Server side Languages we use such as Java, C++, Javascript,python,etc. If server side language is fast then we can implement the Rate Limiting on the server side. Because Rate-limiting formula would be easily implemented. So if language is fast then rate-limiting calculate fast and if slow then it will calculate then sends to the server. whcih increse the latency of the server. 
2. Generally 3rd party rate-limiter providers : If we use that then we can Implement on the api gateway level. And that 3rd party application will manage that. 

-> Rate Limiting processing would required some resources,prosessing capabilities, etc So We can simply use the 3rd party application where we do not need to write Algorthms. 

### Rate Limiting on what?? or On what basis we do the rate limiting ?
- We have three ways to do rate-limiting :- 
  1. Ip Address basis
  2. Request Basis (userID based)
  3. Other ways

  - So on server we have two type of request http/https. So How server will identify that Request coming to it, is unique? 
  - So if the client sends the request r1,r2 and r3 so if the client of the request is same then Ip of the request will be same.
  - Lets say we do the rate limiting on the basis of UserId. similarly we can have different ways exist like addressing. Basically Uniqely identify, property  can be the basis of rate-limiting. 

### What happend when rate-limiting is done?
- Structure 
  - Client
  - server : Rate-Limiting

- Suppose A client send the request to the server where the rate-limiting is already attached with server. So server check that the request is not greater than the timeframe we designed. -> If decided 2 req/sec allowed on the basis of the userId. 
- So if crosses the limit then rate-limiter has to discard this request. So we have two ways to do so :- 
 1. Simply Drop the request
 2. Sends the response of 429 -> which means too many request. 
  - Second is the best way to discard the request so user got notified about their request. We can send some information about how many it can send the request, about duration,etc in the headers. 
  - So ye jo discard messages hai ye rate-limiter he kar raha hai, server involved nahi hai.    

 ->3rd way :- Now we donot want to drop the request, we want to store the request and process the request when the server is free. 
  - So we can store the request which we want to drop we put in the message queue. When the server is free then we give the request from the message queue to the server. 
  - The high priority request will be go the message queue -> Server should have the intelligant logic to decide, to deal with request drop like want to sends 429 message , message queue,etc  

### Algorithums of rate- limiting.
1. Token Bucket
2. Leaky Bucket
3. Fixed Window Counter
4. Sliding window log
5. Sliding-window Counter

- All these algorithun has its own advantages and disadvantages, and different algorithum uses algorithum on the basis of its own usescase. 

#### 1. Token Bucket Algorithum :- 
  - So we gave many request called as request pool, So these request are sends by the many clients and then we got these pool. 
  - Middleware were algorithum, implemented. 
  - Server exist, where we need to send the request for processing. 

  - Logical Structure :-
    - Request Pool
    - Middleware
    - Server
  
  - Structure :- 
    - Request Pool
    - Bucket
    - Token Builder
    - server
  
  - Tokens based Algorithum state that we have one bucket, and we have tokens which comes to this bucket on fixed interval. 
  - So the size of the bucket is fixed, lets bucket size is 3 then 3 tokens can come to this bucket.
  - And we have Token builder which release the token in fixed interval to the bucket. Also called the frequency of the token.
  - We have : Bucket Size : 3 , Inflow of token-builder : 2 token/sec
  - So each second token-bulder releases 2 token to the Bucket. Till here we have seen what we have in the algorithum. 

  Token Algorithum steps :- 
   1. Token builder will push tokens in fixed interval
   2. If bucket size is full then all the new tokens will be overthrown(overflow). 
      - So if the bucket is full then the token generate in the next second will be overflowed/ droped from the bucket. 
   3. Whenever a req comes it taken from the bucket if available and go to the server.
   4. If bucket is empty, req is droped.  

  - Used by the Amazon.
  
  Token Algorithum working :- 
   - When we have any request, its takes the token from the bucket(if token available), and then rate-limiter allow that request to go the server. 
   - Similarly another request will come and take token go to the server so on till token present in the bucket. 
    Note:- All this happen in one sec as in another second we will have two token in our bucket. 
  - If the bucket it empty then it would do -> three thing with request we have seen above. 

 Pros :-
  1. Simple to implement
  2. Can handle the burst traffic for small duration. 
   Burst Traffic :- If server recives to many request then in this case, then starting mai jitna bucket ka size hoga utna request ko server ke pass bhej dega uske baad jitna uska inflow hai, means jitna capacity token per sec hone ka utna he request pass karega.
   - so server ke according ham apne bucket aur inflow ko set kar sakte. 

 Cons :- 
  1. How to decide the bucket capaicity and inflow? -> Need for info about the server to decide. 

#### 2. Leaky Bucket Algorithum :- 
  - Structure :- 
    - Request Pool
    - Bucket (logically a queue)
    - server

  - So this bucket has also some capacity lets say 5. 
  - Here we have the outflow : 2 req/sec

  Working :- 
  - So whenever a any request comes then it goes to this queue. 
  - And we have defined the outflow which bind the queue, the request will go to server, on the basis of outflow we defined. 
   - So ek sec mai hamari queue se 2 request he jayega server tak.
  - So agar bucket full hai then request drop ho jayegi -> to drop req we have three way to do so.  

  - Burst traffic ko handle nahi karta, because hamare pass bahut saari request aa sakti hai lakin hamara server utna he request handle karega jitna uska outflow hai. So ye apne he speed se request ko handle karega ekk sath jayda logo ko handle kar ranho paa raha hai. 
  - used by Shopify.  

  Pros :- 
   - Simple to implement.
   - Even the burst traffic is there, our server will not crash. 

  Cons :- 
   - If DOS/DDOS happens, our server misses valuabale Request. (Main request got starve)

  ##### Problems discussing for implementing:- 
  1. Generally we do rate limiting on the user level or IP level, If rate limiting we do on global level then in this if any user sends the request it got full the size we decide for queue.
   - So this is not good way to do, so we do the rate limiting on userID or Ip addressing based.
   - If we do the rate-limiting on userId basis then we have to make that no of bucket. Because each id has its own bucket. -> So for both Token and Leaky bucket we have this problem. 
   - Ip we do the Rate-limiting on IP level, it will become the more complex and we do ont when the IP will be hit (unable monitor), Every unqiue Ip ke we need to have the rate-limiting -> bucket.
  
  Note : So we know that token based algorthum can handle the Burst traffic, because ye utne request ko handle kar raha hai, jitna token hai, hamare pass. So the Alorithum which handle the burst traffic can also able to handle the DOS/DDOS attack.    

  2. We know that in application we have many api lets say 100. So these api should have their own rate-limiting. So some od the api are simple and work internally and some uses the 3rd party application -> for which we have to give the cost. 
  - So some times we have the API wise rate limiting. 
  - This may also one way to distingush the rate-limiting. 

#### 3. Fixed window Counter Algorithum
 1. In a fixed interval (say a sec, a minute), Only a specific no. of request can come. 
 2. Fixed Counter = 3. 
 3. Request more fixed counter in a particuar time will be dicard. 
 4. And we can handle more request in the next interval time. 

 - So yaha no of request ko handling ko fixed kar diya gaya hai. 
 - So isko samjhne ke liye we take as a global level and but we can do it on user and Ip level. 

 - Cons :- 
   - Are applicable where we do not got the burst traffic. So if interval khatam hone wala the tabhi hamne fixed counter itna request bhej diya, and next interval starting mai fixed counter itna request aa gaya then -> during this interval we have that no. of request which can not be handled by our server. 
    - So ye counter ko fixed window rakhta hai, so for some time of interval we got the many request. 
    - So on the edges(jaha ek interval khatam aur dusra start ho raha hai) chances of the server crash is more. 
  Simply we can say: if burst traffic comes at the edges of window, it may lead to server crash/ high latency. 

  Note : We define fixed counter, on the basis of server capacity to handle the request in the real-world. 

#### Sliding window log algorithum :- 
 - Best algorithum for Rate-limiting as it strict. and Drawback is that it is slow and memory consuming. 
 - This alorithum work on the drawback of the Fixed window Counter Algorithum. So there we are having fixed time period, and at the edge we are having the double edges.  

- In this algorithum, the length of the window is not fixed, it is sliding. 

- Algorithum :- 
  1. It will  store each request in a log fille.
  2. Whenever a req comes, it will first remove all the outdated req from the file which comes in the previous duration of time.  
  3. Then it will log the new req and chech if the counter limit reached then drop the request else send it to server. 

 Working:- 
  - say it will taking 3 req/min.
  - Then if request comes at (00:01) at first sec of the time duration we have. During this our algo allow the request to go the server.
   - Now we If another req comes at 00:30, So we are having request on 30th sec So we check that ki hamare file koi bhi outdated request hai kya, so this is not as first req comes at 1 sec and this will at 30. 
   - If another request comes to the 00:59 sec par then it will allow this to the server. 
   - If another request come at 01:02 then 00:01 request become the outdated. 
   - If another request come to the 01:04 then request come to the window during when req got log in the file it also check the no of request if it exceed then jo abhi request aaya tha usko drop kar dega. 

   - It uses the sliding window technique we have learnt in the DSA. 
  
  - So memory consumming because we are log every request, then our log file size is increased, then memory uses will increased. And it slow beacuse it check for the outdated request and and then no of request count. Then sends to the server or drop the request.  

5. Sliding window Counter Algorithum :- 
  -  It is the hybrid approch of above two.   
  - So in the in sliding window we have to log the request and in the fixed window counter we are having the multiple request are handled together. So what if we we use the best of both. 

  - 5 req/minute allowed -> we are using the fixed window req/vs time graph
  - so here it uses the sliding window not fixed window. 
  - So now if we have the requests at the edge of the time duration, -> all the request are come, which may come during this duration period i.e 5. 
  - At the starting of the second duration if we got the some request then 
    - If we use the fixed window counter then it will allowe the request as the request come in another duration and in duration (5 req/min) request alloed.
    - If in this technique we use the rolling window. means jaha se first request start hua waha se abhi jo request aaya hai waha tak ek rolling window hoga.
     - Rolling uses formula:- 
       No. of req in prev interval + { No. in current interval * Request duration percentage of Previous duration or overlapping ratio(pahle wala duration kitna aa raha hai current duration mai)}

       - Iska answer agar hamre no. of request allowed in duration se jayda hai then request ko drop kar dega. 

#### Create our own rate limmitter :- 
  - Which algo ti implement? 
  --> Fixed window counter

  - Where to store this counter on architecture level :-
     expected Counter = 3
     current counter = 0 
     - jaise jaise value aayegi waise waise value incre / decre karte rahenge

  - Why not store in DB? Why in Cache?
     - Not in DB because it is slow. We donot want that rate-limiter query to DB again again which make it slow. 
     - So we store in cache (best when use the In-memory cache : Radis)
       - Radis is In-memory cache and the storing items are accessible very fast, easily.

  - Where to implement Rate-Limiting? Server or Middleware

Rate-limiter architecture :- 
1. Client(-> rate-limiter)
2. Server 
3. Middleware (Rate-limiter implemented here)
4. Radis (<- rate-limiter)
5. Cache (<- rate-limiter)
6. Workers 
     1. between Db and Cache
     2. between messaging queue and server
7. Messaging queue
  
- Flow of request? 
  - Client sends the request to the rate-limitter and rate limiter decide what to do with the rate-limiter? To drop req or Sends to server.
- To Decide to drop or sends to server on the basis of algo we using i.e fixed window counter. 
- For each userId we will have the a fixed window counter. which we store in the Radis.

- Now when request come to the rate-limiter then it asked the radis about the request that is this req should passed to server or should drop. 
 - Where radis store the info about the counter say current counter is 2 and 3 req are allowed per minute then radis sends response (ok) to rate-limiter and update the conter. 


After the complitation of the duration we want wo purane wale request ko remove karna hoga then we have again do counter = 0. So how can we do this Radis. 
 Radis has a two function :- 
  1. INCR : 
  2. EXPIRE :

  - so agar ham koi value radis mai dete hai then ham ye bhi fixed kar sakte hai ki expire kab hoga, lets say expire = 1 min , then 1 min baad value expire kar dega. 
  - INCR : increse the value by one. 

Rules stored in the database :- 
 - like drop request, etc
 - Lets say we stored the rules in DB, So firstly rate-limiter fetch the rules from the database. 
 - New radis info ke rule ke basis par decesion lenge ki request ke sath kya karna hai.

 - So if we use DB then rate-limiter will be slow-down then we have to introduced new cache 
 - or we can store it in the radis so we know that Radis is In-memory, so it is vilatile memory. So it data got erase and then we have to load the rules again in the Rate-limiter. 

 - We introduced cache, it works is to fetch the rules from the DB and and rate-limiter will communicate with this cache. 

  Request flow
  - Client request go the rate-limiter than it sends request to the radis to have the details about the current counter, then radis send the response to rate-limitter and then rate take the asked for the rules to the cache if the cache has the reules it response if cache has not rules then cache req to db and db response it back and the cache response to rate-limiter. 
  - So if rules satisfied then it sends the request to the server if not then it drop the message. We can also send this request the messaging queue (Kafka) so if the server is free then we can send this req to the server. So we need to add one more workers between server and messaging queue -> jiska kaam hoga ki jab server free ho toh req ko server ke pass bhej do

  Note :- Only High priority request will be send to the messaging queue.  

Workers :-  Iska kaam hota hai ki time to time DB se rules ko fetch karke cache ko dena.For now can understand that workers is server which fetch the rules from the server and delivered it to the cache. 

Summary :- 
Request flow :
 1. Client sends the request to the rate-limitter
 2. Rate-limiter has four possible things to do with request :- 
    1. Send it to the server
    2. Drop the message
    3. Return 429 message to the client
    4. Store req in Messaging queue 
 3. What rate-limitter will do with req will depends on two things :- 
    1. Rate limiting rules we decide
    2. and the current counter value

    - It will hot the rules from the cache and From where cache got the rules -> so then we introduced the server, which time to time fetch the rules from DB and send back to the Cache. (Time din mai ek baar or hafte mai ek baar)
    - So if the rate-limiter got the rules then it ask to the radis for the counter and  where we radis do the 1 increment and send back to the rate-limitter

4. On the basis of rules if we cannot give req to the server then we have three way :- 
   1. Drop
   2. 429
   3. Message queue

   - agar high priority queue hai then isko kaise req high-priority queue banti hai. Req ke header ek property bana sakte hai.  