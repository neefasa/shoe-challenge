Fall 2021 Data Science Intern Challenge 

Submission by: Nicholas Foster

Email: nkfoster at gmail dot com



**Question 1:**

My suspicion is that an AOV of $3145.13 was a result of using the mean average when significant outliers are present in the data. 

Summary Statistics for Order Amount:

* Mean = 3145.13
* Standard Deviation = 41282.54
* Median = 284
* Interquartile range = 163 - 390

The large mean and standard deviation point to the presence of outliers. After exploring the data, there were two main contributors to the the extreme AOV. 

**Customer with Irregular Behavior ** - The customer with user_id = 607 at shop_id = 42 would order 2000 units on a regular basis. 

**Store with Luxury Goods**  - The store with shop_id = 78 was selling shoes that cost 25,725 per unit. There may be an error in the system - e.g. the currency for this shop isn't actually USD. It is also feasible that this shop is selling luxury sneakers. 

If this one shop and one customer are removed, the mean becomes 302.58, which is more in the realm of what is expected of an average order value for this data. Interestingly, the median after the removal did not change. It remained at 284. 

**Choice of Metric**

If a metric is desired to find the central tendency of order amounts at sneaker stops, the simplest solution is to use the median average rather than the mean average. The median order amount for the data is **$284**. The median average is a common metric that people would have some familiarity with. A more complicated metric would require more explanation. 



**Question 2:** 

How many orders were shipped by Speedy Express in total?

*54*

```sql
SELECT COUNT(*) total_orders FROM orders
WHERE ShipperID = 
	(SELECT ShipperID FROM Shippers
	 WHERE ShipperName = 'Speedy Express');
```

**What is the last name of the employee with the most orders?**

*Peacock*

```sql
WITH top_employee as (
	SELECT o.EmployeeID, FirstName, LastName, COUNT(OrderID) order_count
	FROM Orders o
  LEFT JOIN Employees e
  	ON o.EmployeeID = e.EmployeeID
	GROUP BY o.EmployeeID
	ORDER BY order_count DESC
	LIMIT 1
    )

SELECT LastName FROM top_employee;
```



**What product was ordered the most by customers in Germany?**

*Gorgonzola Telino*

There was a little ambiguity to the question. It could be asking which product was in the most orders or which product had the most units order.  I chose the former. Although knowing the total amount of product being order is something that may be of interest, being able to make an equivalent comparison is complicated by the unit size and cost. The product with the most units ordered could be found by using the same query if COUNT is changed to SUM in the SELECT statement.

```sql
SELECT p.ProductName, COUNT(od.Quantity) amount
FROM OrderDetails od
LEFT JOIN Products p
	ON od.ProductID = p.ProductID
LEFT JOIN Orders o
	ON od.OrderID = o.OrderID
LEFT JOIN Customers c
	ON o.CustomerID = c.CustomerID
WHERE c.Country = 'Germany'
GROUP BY od.ProductID
ORDER BY amount DESC
LIMIT 1;
```

