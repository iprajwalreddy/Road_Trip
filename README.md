Road trip: In this question we have been given a dataset of major highway segments of the United States, which includes highway names, distances, and speed limits on the respective paths. Along with this we have also receive a dataset of cities and towns with corresponding latitude-longitude positions. 

We need to find driving directions between the start city and end city entered by the user while optimizing for distance/ time/ delivery-time/ segments traversed. We also have to return the *"total-segments", "total-miles", "total-hours", "total-delivery-hours", "route-taken"* while optimizing for each paramter. 

The user also selects the cost function they want to optimize for. ie. *distance, time, segments, delivery.*

### **Initial State:** 
- The start city that is given as input from which we need to find the best route to the destination city. This start city will be considered as our initial state.

### **Valid states**
- All the existing paths between two given cities are considered as valid states.

### **Successor Function:**
- Returns all the cities that can be reached from the current city. (We also ensure that a previous visited city is not generated as a part of the successors, but this is only when we generate successors. *It is possible to come across the same state multiple times with a different possible f score*)

### **Goal State:**
- The destination state i.e The end city given by the user. We provide giving the optimal results based on the cost function that we gave input, i.e we need to display the best route, with the best distance/ number of segments/ time to reach the destination/ the delivery time taken between the 2 cities.

### **Cost Function and Heuristic explanation:**

 The Cost Function(g) is the numerical value from the start state to the current state. The Heuristic cost(h) is the estimated numerical value from the current state to the goal state.

- Distance:
    - Cost(g): The cost function is the distance from the start city to the current city in the route. It is calculated from the 'road-segments.txt' file which has the details of the highways from various cities along with the distance in miles.
    - Heuristic(h): The heuristic is the estimated distance from the current city in the route to the end city. The estimated distance from a particular city is calculated as an 'euclidean distance' using latitiude and longitude. Wherever the latitude and longitude details where unavailable, we calculated the heuristic by subracting the cost value from the previous heuristic. This gives an approximate distance of the current city or junction from the end city.


- Time:
    - Cost(g): The cost function is the time taken to travel from the start city to the current city. The time taken is calculated as distance divided by speed. Distance and speed are obtained from the 'road-segments.txt' file which had the distance in miles and the speed limit in miles per hour. It was assumed that we are travelling at the uniform max speed limit of the particular highway.
    - Heuristic(h): The heuristic is the estimated time taken to travel from the current city to the end city. The estimated time is calculated again as distance divided by speed. The distance was calculated as 'euclidean distance' using latitude and longitude from the 'city-gps.txt' file which has the city name along with the latitude and longitude. For the cities or junctions for which the latitude and longitude were unavailable, the distance was calculated by calculating the total_distance between the start city and end city, and then subracting from it the distance which had been travelled till the current city. We then took this distance and divided it by the maximum speed limit.


- Segments:
    - Cost(g): The cost function is the number of segments it takes to travel from the start city to the current city. The number of segments is calculated by adding one each time we transverse a route.
    - Heuristic(h) : The heurisitic function is the estimated number of segments it takes to travel from the current city to the end city. We took the heuristic function for segments to always be '1'. We are assuming that the number of segments it takes to reach the end city is 1 from the current city.


- Delivery Time:
    - Cost(g): To minimize the amount of time taken for delivery, we need to make sure that the amount of time taken  is less. Hence, we choose the same calculation as of time.
    - Heuristic(h): We calculate the amount of delivery time it takes by using the distance and speed limit. Here also we assume that the truck travels at uniform maximum speed limit. Hence, we calculate the estimated delivery time by using the formulaes from the question.

### **Assumptions**
- We assumed that the vehicle travels at uniform maximum speed limit for that particular highway.
- We also assumed that all the highways allow the vehicles to travel in both the directions and none of them are one ways as mentioned in the question.

### **Code provided:**
- The skeleton code was initially returning a static best route between Bloomington,_Indiana Indianapolis,_Indiana , along with total segments taken , total miles , total hours , and total delivery time between the input cities.

### **Approach:** 
- In this code we have again used the A* algorithm yet again (Further explained below). We store the city-gps.txt data  into a dictionary named city_lat_long , which contains the city names (as keys), latuitude and longtiude of the city in form of a tuple (as value).

- We have stored the road-segments.txt into a dictionary named road_segments , containing  one line per road segment connecting two cities.
The space delimited fields are:

    - first city
    - second city
    - distance (in miles)
    - speed limit (in miles per hour)
    - highway name

- From the above point, first city and seconds city stored in a tuple serve as a key, the value is a dictionary consisting of distance, speed limit and highway as keys and their corresponding values as the values of the dictionary.

- In attempt to make the code *modular*, *readable* and more *convenient* to use we have modeled each state as an object belonging to the class **'State'** ( following the principles of **Object oriented programming** ) having attributes relevant to states such as the path to the state, source city, destination city, speed limit for current route from source to destination city, highway name, f(n), g(n) and h (n) .etc. 

- We have also overriden the \__eq()__ class method in order to make comparision of object classes easier. In fact this is where we handle the case of discard duplicate states that are already present in visited list or priority queue but have a worse f value.

 - As mentioned above, the approach we used was a A* algorithm and various heuristic functions for distance, segments, time and delivery time (As explained above). In the successor function, we return all the neighbouring paths that are possible from our current location as State objects. 
 
 - We then append all the successor objects we receive into a priority queue (Ordered by the *'f'* score) and then compute the heuristic values for all of them and select the best successor (pop the successor with least f score out of the priority queue) with the least f(n) score for exploration. (This is handled by the inherent ordering of a priority queue : We used a list and repeatedly sorted it based on f as we built a custom class to store states).

- Whenever we get a successor that is the best move according to our heuristic (present at the top of the priority queue) we go to that move for exploration and this repeats until we reach a goal state.  

- When appending our new successors to the priority queue, we always ensure that if this successor is already in the priority queue (same board state), we only append if the current f is lower than the f of the existing same state in the priority queue. 

- There is also a visited list, which consists of visited states, if the f of those successors which have the same board state is lower than the f of the current successor then we skip this successor, else we append it.  

- This process is repeated until we reach the goal state.
