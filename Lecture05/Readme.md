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

1. Token Bucket :- 
  - So we gave many request called as request pool, So these request are sends by the many clients and then we got these pool. 
  - Middleware were algorithum, implemented. 
  - Server exist, where we need to send the request for processing. 

  - Structure :-
    - Request Pool
    - Middleware
    - Server
  
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
