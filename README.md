Hello Gabriel,
It was pleasure to speak with you today, here is the solution.
Took me couple more minutes to put it up here.
Enjoy!

## Algorithm link:

[https://codesandbox.io/s/building-an-ioc-container-demo-start-forked-4bzu1z](https://codesandbox.io/s/building-an-ioc-container-demo-start-forked-4bzu1z?file=/src/test.spec.js)

You can run the tests there to see that they all pass.

Do not mind the slug name it's for a different fork, i just reused it for speed.

## Application Design:
```
condos: {
  id: int
  name: string
}

floorplans: {
  id: int
  name: string
}

units: {
  id: int
  condo_id: int (reference to condo)
  floorplan_id: int (reference to floorplan)
  unit_number: int,
}

Units sample data:
id | condo_id | floorplan_id | unit_number
1, 1, 1, 101,
2, 1, 2, 102,
3, 1, 2, 202,
4, 1, 2, 303,
5, 1, 3, 403,


prices: {
  id: int,
  unit_id: int,
  price: number,
  is_current_price: boolean,
  created_at: timestamp/datetime
}


function basePrice(pricePerSqft, sqft){
  return pricePerSqft * sqft
}

function priceAddonPerFloor(addOnPrice, unitNumber){
  const realFloor = parseInt(String(unitNumber).charAt(0)) - 1
  // floor 1 is actually floor 0 for our calculation
  return realFloor * addOnPrice
}

function totalPrice(pricePerSqft, sqft, addOnPrice, unitNumber) {
  return basePrice(pricePerSqft, sqft) + priceAddonPerFloor(addOnPrice, unitNumber);
}


function createUnit() {
  unit = insert_into_units_table([
    'condo_id' => int,
    'floorplan_id' => int,
    'unitnumber' => int,
  ])
  insert_into_prices(totalPrice(...), datetime, is_current_price = true)
}

function updateUnit() {
  update_units_table([
    'condo_id' => int,
    'floorplan_id' => int,
    'unitnumber' => int,
  ])

  if (price_changed) {
    insert_into_prices(totalPrice(...), datetime, is_current_price = true)
    make_previous_price_not_current_price()
  }
}

function deleteUnit() {
  delete from units table based on id
  cascade the database to delete unit prices from prices table for that unit
  or delete those records via application code
}

function viewPricesOfUnits(){
  get units table joined to the prices table on is_current_price=true flag to get the current prices of units
    or group by a different date range to see prices of units for that date range, group by condo_id
}

function viewSumUnitPrices(){
  get units joined to the prices on is_current_price=true flag, group by condo_id,
    + run select with SUM(price) to get the current total prices for all units

  if you need prices by particular date or to create the view visualization, join by price on date range
}
````

## System design
````
- Create database using MySQL, MariaDB, PostgreSQL or a autoscalable solution by Amazon Aurora, Planet Scale from Google, or TIDB for RDBS support
- Setup Load Balancer with static IP
- Setup HTTPS for the load balancer IP and point the domains at the load balancer
- Setup Ubuntu/CentOS/Alpine/Linux servers with NGINX or NodeJS under the instance groups
- Link the instance group to the load balancer
- Setup https for individual servers to prevent Google/AWS man in the middle attack from Google/AWS itself
- Make those servers auto scalable via CLOUD if we are using a distributed database solution like Amazon Aurora, or Planet Scale,
  if not we can implement custom scalability solution with database master/slave replication
- Create an API server using Laravel/ExpressJS, in Laravel case create quickly the API through Laravel Orion ORM extension standard library
- Setup Token Authentication using Bearer token system using Laravel Sanctum, so only authorized users can send and receive the API requests
- Standardize the API with try {} catch {} blocks to catch and log errors and have correct API responses to each request 200,400,401,403,406,500
(etc)
- Output OpenAPI spec for the API so that everyone is aware of the open/closed API endpoints
- Run clockwork or any other type of logging to log the speed of the requests to optimize the performance of the API

Funny thing I've implemented a basic API that syncs data with Shopify using laravel that you can take a look at just last week,
  it was a task for one of other potential employers, where I implemented the whole API from scratch, and you can even see it operational online,
  check this repository:
````

https://github.com/infinite-system/shopify

They are in the process of reviewing it, and you can review it as well.

Regards,

Evgeny Kalashnikov
