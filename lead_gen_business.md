Hint: Here is an example of a query that selects data between a date range

SELECT Date,TotalAllowance 
FROM Calculation 
WHERE EmployeeId=1 AND Date >= '2011/02/25' AND Date < '2011/02/28'

1. What query would you run to get the total revenue for March of 2012?

SELECT billing.charged_datetime, billing.amount
FROM billing
WHERE charged_datetime >= '2012-03-01' AND charged_datetime <= '2012-03-31';

2. What query would you run to get total revenue collected from the client with an id of 2?

SELECT clients.client_id, clients.first_name, clients.last_name, billing.amount
FROM billing
JOIN clients ON clients.client_id = billing.billing_id
WHERE clients.client_id = 2;

3. What query would you run to get all the sites that client=10 owns?

SELECT clients.client_id, clients.first_name, clients.last_name, sites.site_id, sites.domain_name
FROM sites
JOIN clients ON clients.client_id = sites.client_id
WHERE clients.client_id = 10;

4. What query would you run to get total # of sites created per month per year for the client with an id of 1? What about for client=20?

SELECT clients.client_id, COUNT(sites.domain_name) AS number_of_websites, MONTHNAME(sites.created_datetime), YEAR(sites.created_datetime)
FROM sites
JOIN clients ON clients.client_id = sites.client_id
WHERE clients.client_id = 1
GROUP BY MONTH(sites.created_datetime), YEAR(sites.created_datetime);

SELECT clients.client_id, COUNT(sites.domain_name) AS number_of_websites, MONTHNAME(sites.created_datetime), YEAR(sites.created_datetime)
FROM sites
JOIN clients ON clients.client_id = sites.client_id
WHERE clients.client_id = 20
GROUP BY MONTH(sites.created_datetime), YEAR(sites.created_datetime);

5. What query would you run to get the total # of leads generated for each of the sites between January 1, 2011 to February 15, 2011?

SELECT sites.domain_name, COUNT(leads_id) AS number_of_leads, DATE_FORMAT(leads.registered_datetime, "%M %d %Y") AS date_generated
FROM sites
JOIN leads ON sites.site_id = leads.site_id
WHERE leads.registered_datetime >= '2011/01/01' AND leads.registered_datetime <= '2011/02/15'
GROUP BY sites.site_id;

6. What query would you run to get a list of client names and the total # of leads we've generated for each of our clients between January 1, 2011 to December 31, 2011?

SELECT CONCAT(clients.first_name, " ", clients.last_name) AS client_name, COUNT(leads_id) AS number_of_leads
FROM sites
JOIN leads ON sites.site_id = leads.site_id
JOIN clients ON clients.client_id = sites.client_id
WHERE leads.registered_datetime >= '2011/01/01' AND leads.registered_datetime <= '2011/12/31'
GROUP BY client_name;

7. What query would you run to get a list of client names and the total # of leads we've generated for each client each month between months 1 - 6 of Year 2011?

SELECT CONCAT(clients.first_name, " ", clients.last_name) AS client_name, leads.leads_id AS number_of_leads, MONTHNAME(leads.registered_datetime) AS month_generated
FROM sites
JOIN leads ON sites.site_id = leads.site_id
JOIN clients ON clients.client_id = sites.client_id
WHERE leads.registered_datetime >= '2011/01/01' AND leads.registered_datetime <= '2011/06/30'
GROUP BY clients.client_id, MONTH(leads.registered_datetime);

8. What query would you run to get a list of client names and the total # of leads we've generated for each of our clients' sites between January 1, 2011 to December 31, 2011? Order this query by client id.  Come up with a second query that shows all the clients, the site name(s), and the total number of leads generated from each site for all time.

SELECT CONCAT(clients.first_name, " ", clients.last_name) AS client_name, sites.domain_name AS website, 
COUNT(leads.leads_id) AS number_of_leads, leads.registered_datetime AS date_generated
FROM sites
JOIN leads ON sites.site_id = leads.site_id
JOIN clients ON clients.client_id = sites.client_id
WHERE leads.registered_datetime >= '2011/01/01' AND leads.registered_datetime <= '2011/12/31'
GROUP BY website
ORDER BY clients.client_id;

SELECT CONCAT(clients.first_name, " ", clients.last_name) AS client_name, sites.domain_name AS website, 
COUNT(leads.leads_id) AS number_of_leads
FROM sites
JOIN leads ON sites.site_id = leads.site_id
JOIN clients ON clients.client_id = sites.client_id
GROUP BY website
ORDER BY clients.client_id, number_of_leads DESC;

9. Write a single query that retrieves total revenue collected from each client for each month of the year. Order it by client id.

SELECT CONCAT(clients.first_name, " ", clients.last_name) AS client_name, billing.amount AS revenue, 
MONTHNAME(billing.charged_datetime) AS month_charge, YEAR(billing.charged_datetime) AS year_charge
FROM clients
JOIN billing ON clients.client_id = billing.client_id
GROUP BY month_charge, year_charge
ORDER BY clients.client_id;


10. Write a single query that retrieves all the sites that each client owns. Group the results so that each row shows a new client. It will become clearer when you add a new field called 'sites' that has all the sites that the client owns. (HINT: use GROUP_CONCAT)

SELECT CONCAT(clients.first_name, " ", clients.last_name) AS client_name, GROUP_CONCAT(sites.domain_name) AS sites
FROM clients
JOIN sites ON clients.client_id = sites.client_id
GROUP BY client_name
ORDER BY clients.client_id;