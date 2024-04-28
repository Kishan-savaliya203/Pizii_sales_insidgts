# Pizii_sales_insidgts

#Questions:

1.Retrieve the total number of orders placed.

    SELECT 
        COUNT(order_id) as Total_orders
    FROM
        orders;

----Total orders=21350

2.Calculate the total revenue generated from pizza sales.


    SELECT 
        ROUND(SUM(quantity * price), 2) AS total_revenue
    FROM
        order_details
            JOIN
        pizzas ON order_details.pizza_id = pizzas.pizza_id;


----Total revenue = 817860.05

3.Identify the most common pizza size ordered.


    SELECT 
        size, COUNT(orders.order_id) AS common_pizza
    FROM
        pizzas
            JOIN
        order_details ON pizzas.pizza_id = order_details.pizza_id
            JOIN
        orders ON order_details.order_id = orders.order_id
    GROUP BY size;

----orders by pizza size = M	15385,
                           L	18526,
                           S	14137,
                           XL	544,
                           XXL	28

4.List the top 5 most ordered pizza types along with their quantities.


    SELECT 
        pizza_types.pizza_type_id, SUM(quantity) AS qunt
    FROM
        order_details
            JOIN
        pizzas ON order_details.pizza_id = pizzas.pizza_id
            JOIN
        pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
    GROUP BY pizza_types.pizza_type_id
    ORDER BY qunt DESC
    LIMIT 5;

----Top 5 most ordered pizza types = classic_dlx	2453,
                                     bbq_ckn	2432,
                                     hawaiian	2422,
                                     pepperoni	2418,
                                     thai_ckn	2371

5.Determine the distribution of orders by hour of the day.


      SELECT 
          HOUR(orders.order_time) AS time,
          COUNT(order_id) AS total_orders
      FROM
          orders
      GROUP BY time
      ORDER BY time;

----Distribution of orders by hour of the day = 9	1,
                                                10	8,
                                                11	1231,
                                                12	2520,
                                                13	2455,
                                                14	1472,
                                                15	1468,
                                                16	1920,
                                                17	2336,
                                                18	2399,
                                                19	2009,
                                                20	1642,
                                                21	1198,
                                                22	663,
                                                23	28

6.Group the orders by date and calculate the average number of pizzas ordered per day.


    with cte1 as(
    select order_date,sum(quantity) as total_quantity
    from orders
    join order_details
    on orders.order_id=order_details.order_id
    group by order_date)
    
    select round(sum(total_quantity)/count(total_quantity),2) as avg_sold_quantity_per_day
    from cte1;

----average number of pizzas ordered per day = 138.47

7.Calculate the percentage contribution of each pizza type to total revenue.


    SELECT 
        category,
        ROUND(SUM(quantity * price) / (SELECT 
                        revenue
                    FROM
                        sk1) * 100,
                2) AS revenue
    FROM
        pizzas
            JOIN
        order_details ON pizzas.pizza_id = order_details.pizza_id
            JOIN
        pizza_types ON pizza_types.pizza_type_id = pizzas.pizza_type_id
    GROUP BY category;

----persentage contribution in total revenuee by category = Classic	26.91%,
                                                            Veggie	23.68%,
                                                            Supreme	25.46%,
                                                            Chicken	23.96%

8.Find the cumulative revenue generated over time.


    select rvn.order_date, round(sum(rvn.revenue) over(order by order_date),2) as cum_revenue
    from 
    (SELECT 
        order_date, ROUND(SUM(quantity * price), 2) AS revenue
    FROM
        pizzas
            JOIN
        order_details ON pizzas.pizza_id = order_details.pizza_id
            JOIN
        orders ON orders.order_id = order_details.order_id
    GROUP BY order_date) as rvn;

----    2015-01-01	2713.85,
        2015-01-02	5445.75,
        2015-01-03	8108.15,
        2015-01-04	9863.6,
        2015-01-05	11929.55
        ....
        ....
        ....

                                                            
