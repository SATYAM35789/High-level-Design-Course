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


