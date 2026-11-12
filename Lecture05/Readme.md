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

  