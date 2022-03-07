# AssignmentSQL_OrderDataModel
Executed various SQL queries to fetch data from different tables of Order Data model of Moqui.


1. List all columns of OrderItem table.</br>
SELECT * FROM assignment.order_item;

2. List the person details for all females.</br>
SELECT * FROM assignment.person WHERE GENDER="F";

3. List the Order Header table contents so that the latest placed order appears in the top.</br>
SELECT * FROM assignment.order_header ORDER BY PLACED_DATE DESC;

4. List the Person details with MarsMarried marital status.</br>
SELECT * FROM assignment.person WHERE MARITAL_STATUS_ENUM_ID="MarsMarried";

5. Output the count of Order Parts for each shipment Method Enum Id.</br>
SELECT SHIPMENT_METHOD_ENUM_ID, COUNT(*) FROM assignment.order_part GROUP BY SHIPMENT_METHOD_ENUM_ID;

6. List the Order Header details with grand total greater than 50.</br>
SELECT * FROM assignment.order_header WHERE GRAND_TOTAL>50;

7. List the Person details who have not placed any orders.</br>
SELECT * FROM assignment.person WHERE PARTY_ID NOT IN (SELECT CUSTOMER_PARTY_ID FROM assignment.order_part);

8. List the Person details whose first name starts with ‘J’ and third character is ’h’.</br>
SELECT * FROM assignment.person WHERE FIRST_NAME like "J_h%" ;

9. List all Order Item details for the order with orderId, 100102 and orderPartSeqId, 01.</br>
SELECT * FROM assignment.order_item WHERE ORDER_ID="100102" AND ORDER_PART_SEQ_ID="01";

10. List the Order Item details for only those products for which chargeShipping is set to "Y".</br>
SELECT * FROM assignment.order_item AS t1
JOIN assignment.product AS t2 ON t1.PRODUCT_ID = t2.PRODUCT_ID WHERE t2.CHARGE_SHIPPING="Y";

11. Find order parts with part total less than equals to 20.</br>
SELECT * FROM assignment.order_part WHERE PART_TOTAL<=20;

12. List the Person details whose employment status is either EmpsSelf or EmpsFullTime without using the OR operator.</br>
SELECT * FROM assignment.person WHERE EMPLOYMENT_STATUS_ENUM_ID IN ("EmpsSelf", "EmpsFullTime") ;

13. Find the Order details assigned to the facility with ZIRET_WH value. Output the below fields for this query </br>
SELECT t1.ORDER_ID, t1.ORDER_PART_SEQ_ID, t1.FACILITY_ID, t1.PART_NAME, t1.CUSTOMER_PARTY_ID, t2.PRODUCT_ID, t2.ITEM_DESCRIPTION, t2.QUANTITY 
FROM assignment.order_part AS t1
JOIN assignment.order_item AS t2 
ON t1.ORDER_ID= t2.ORDER_ID
WHERE t1.FACILITY_ID="ZIRET_WH ";

14. Output the sum of the grandTotal from Order Header table for each placed date.</br>
SELECT PLACED_DATE, SUM(GRAND_TOTAL) FROM assignment.order_header GROUP BY PLACED_DATE;

15. List the Order Header records where grand total is greater than highest grand total of the order placed on 2020-04-17.</br>
SELECT * FROM assignment.order_header WHERE GRAND_TOTAL > (SELECT SUM(GRAND_TOTAL) FROM assignment.order_header WHERE PLACED_DATE="2020-04-17");

16. Output the sum of the grandTotal from Order Header table for each placed date where total sum is greater than 60.</br>
SELECT SUM(GRAND_TOTAL) AS s FROM assignment.order_header GROUP BY PLACED_DATE having s>60;

17. Output the Person's party Id, first name, last name and orderId for all customers who have placed orders on 2020-04-17.</br>
SELECT DISTINCT p.PARTY_ID, p.FIRST_NAME, p.LAST_NAME, op.ORDER_ID
FROM assignment.person as p
JOIN assignment.order_part as op
ON p.PARTY_ID=op.CUSTOMER_PARTY_ID
JOIN assignment.order_header as o
ON op.ORDER_ID=o.ORDER_ID WHERE o.PLACED_DATE ="2020-04-17";

18. List the Person details whose order status in Order Part table is OrderApproved using EXISTS operator.</br>
SELECT * FROM assignment.person
WHERE EXISTS
(SELECT STATUS_ID FROM assignment.order_part WHERE STATUS_ID="OrderApproved");

19. List the Order Header details for highest grandTotal for each placed date.</br>
SELECT MAX(GRAND_TOTAL) FROM assignment.order_header GROUP BY PLACED_DATE;

20. Find the count of order parts for each Person. Output the below details for this query:
partyId
firstName
lastName
count</br>
SELECT p.PARTY_ID, p.FIRST_NAME, p.LAST_NAME, COUNT(o.ORDER_PART_SEQ_ID)
FROM assignment.person as p
JOIN assignment.order_part as o 
ON o.CUSTOMER_PARTY_ID = p.PARTY_ID
JOIN assignment.order_header AS oh
ON o.ORDER_ID=oh.ORDER_ID GROUP BY CUSTOMER_PARTY_ID;

21. Output the customerPartyId and highest order grandTotal for each customer.</br>
SELECT op.CUSTOMER_PARTY_ID, MAX(GRAND_TOTAL)
FROM assignment.order_header as oh
JOIN assignment.order_part as op
ON oh.ORDER_ID = op.ORDER_ID
JOIN assignment.person as p 
ON p.PARTY_ID = op.CUSTOMER_PARTY_ID GROUP BY op.CUSTOMER_PARTY_ID;

22. List the unique order Ids for the Order Headers with more than one Order Part.</br>
SELECT oh.ORDER_ID, COUNT(op.ORDER_PART_SEQ_ID)
FROM assignment.order_header as oh
JOIN assignment.order_part as op
ON op.ORDER_ID = oh.ORDER_ID GROUP BY op.ORDER_ID;

23. List all Order Header details whose grandTotal is greater than the average grandTotal for all orders placed on 2020-04-17.</br>
SELECT * FROM assignment.order_header WHERE GRAND_TOTAL > (SELECT AVG(GRAND_TOTAL) AS a FROM assignment.order_header WHERE PLACED_DATE="2020-04-17");

24. List the ItemSalesTax Order Item Details for each order.</br>
SELECT * FROM assignment.order_item WHERE ITEM_TYPE_ENUM_ID = "ItemSalesTax";

25. List the below Order Information for the orderId, 100153.
orderId, orderPartSeqId, customerPartyId, firstName, lastName, placedDate, orderItemSeqId, productId, itemDescription, unitAmount
quantity, partTotal, grandTotal8 </br>
SELECT oh.ORDER_ID, op.ORDER_PART_SEQ_ID, op.CUSTOMER_PARTY_ID, p.FIRST_NAME, p.LAST_NAME, oh.PLACED_DATE, oi.ORDER_ITEM_SEQ_ID, PRODUCT_ID, ITEM_DESCRIPTION,
UNIT_AMOUNT, QUANTITY, PART_TOTAL, GRAND_TOTAL
FROM assignment.order_header as oh
JOIN assignment.order_part as op
ON oh.ORDER_ID = op.ORDER_ID
JOIN assignment.person as p
ON p.PARTY_ID = op.CUSTOMER_PARTY_ID
JOIN assignment.order_item as oi
ON oh.ORDER_ID = oi.ORDER_ID
WHERE oh.ORDER_ID= "100153";


26. List the below Order details for the customer partyId, CustDemo1.
orderId, orderPartSeqId, orderName, partName, customerPartyId, facilityId, orderItemSeqId, productId, itemDescription, quantity
unitAmount</br>
SELECT oh.ORDER_ID, op.ORDER_PART_SEQ_ID, oh.ORDER_NAME, op.PART_NAME, op.CUSTOMER_PARTY_ID, op.FACILITY_ID, oi.ORDER_ITEM_SEQ_ID, PRODUCT_ID, ITEM_DESCRIPTION,
UNIT_AMOUNT
FROM assignment.order_header as oh
JOIN assignment.order_part as op
ON oh.ORDER_ID = op.ORDER_ID
JOIN assignment.person as p
ON p.PARTY_ID = op.CUSTOMER_PARTY_ID
JOIN assignment.order_item as oi
ON oh.ORDER_ID = oi.ORDER_ID
WHERE op.CUSTOMER_PARTY_ID= "CustDemo1";
