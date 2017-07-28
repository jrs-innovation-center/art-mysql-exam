# Art - MySQL

## Objectives

- Create a MySQL data access layer or DAL.
- Effective use of environment variables:
  - Determine which "DAL" (CouchDB or MySQL) the api "talks" to.
  - Hold the values related to connecting to mysql.
- Create a MySQL database and tables using MySQL workbench.
- Utilize data manipulation language statements such as SELECT, INSERT, UPDATE, and DELETE queries.
- Perform row aggregation.  
- Utilize data definition language statements in the form of a sql script file to create the database, tables, views, foreign keys and data.  
- Provide developer documentation to minimize on-boarding friction including instructions on loading a mysql database setup script.  

## Getting Started

1. Create a new `art-mysql-exam` branch within your existing `art-api-exam` repository.  

  >  As you solve a piece of the exam and have the app working without error, remember to commit your changes to your new `art-mysql-exam` branch within your fork.

## Resources

Refer to the following resources and examples for assistance:

 - [auto api example](https://github.com/jrs-innovation-center/auto-api)
 - [instruments api example](https://github.com/jrs-innovation-center/instruments-api/tree/6-my-sql-list)
 - [mysql javascript libary/database driver](https://www.npmjs.com/package/mysql)
 - [MySQL Docs](https://dev.mysql.com/doc/refman/5.7/en/)
 - [HTTP Status Codes](http://www.restapitutorial.com/httpstatuscodes.html)
 - [Scripting the database and data](http://mysql.how2js.com/9-sql-intro/1)
 - [Example script file](https://github.com/jrs-innovation-center/mysql-camp-student/blob/master/sql-scripts/rockstar-database-and-data.sql)
 - [Executing SQL Statements from a Text File](https://dev.mysql.com/doc/refman/5.7/en/mysql-batch-commands.html)

## Steps

Successfully complete the first 5 steps to receive a grade of 'Meets Expectations'. Complete step 6 and 7 to receive a grade of 'Above Expectations'.  Complete step 8 to receive a grade of 'Exceptional'.  

When you have completed the exam, push your changes to the `art-mysql-exam` branch within your fork.  Send a direct message to your instructors containing the url of your repo and branch.  

### Step 1 - Create database

- Create a mysql database named **art**.  

- Create the following table :

  - `painting` - used to track all the paintings.  

- Add data into the table.  Use an integer for your primary key values.

### Step 2 - Environment variables

- Use an environment variable to control which DAL (CouchDB or MySQL) the api code within **app.js** "talks" to.  Provide the ability to switch database platforms when the api starts.  Ex:  `DAL=dal-sql node app.js`

- Use environment variables to manage the values used when creating a connection to mysql including IP address, database name, user name and password.

- Modify your `npm start` script to default to the new sql DAL.

### Step 3 - Create a mysql dal

- Update the code defined in your `art-api-exam` repository's `art-mysql-exam` branch. Create a **dal-sql.js** file used to interact with the mysql database named **art**.

- When calling the API endpoints from POSTman, the JSON key names should remain the same.  You should still display  `_id`, `_rev` and `type` keys in your `GET` responses, for example.  **YOUR PRIMARY KEY VALUES WILL LOOK MUCH DIFFERENT**.  In a my sql database, the primary key values are an integer, while in a CouchDB database, the primary key values are a string.  

  As an example, to retrieve a painting using the API, you would perform a `GET /art/paintings/:id`.  Here's an example of the path that contains the primary key value for a painting with a primary key value of 5 from a mysql database:

  **Sample Request**

  ```
  GET /art/paintings/5  
  ```

- Ensure the following endpoints work for your MySQL database.  

  **Art Paintings**

  - CREATE: `POST /art/paintings`  
  - READ: `GET /art/paintings/:id`  
  - UDPATE: `PUT /art/paintings/:id`
  - DELETE: `DELETE /art/paintings/:id`
  - LIST/FILTER/PAGINATE `GET /art/paintings`


### Step 4 - Create script .sql file

Similar to the previous **baseball.sql** [example](http://mysql.how2js.com/10-joins/1), create an **art.sql** script file to recreate the database with data.  Place the script within the **sql-scripts** folder.

### Step 5 - Getting Started

Modify existing developer on-boarding instructions by updating your **README.md** file.  Be sure to provide developer documentation to minimize on-boarding friction including instructions on loading database setup script. Your IP address to your localhost will vary depending on whether you have a local install or using Docker.  

  Example (MySQL on Docker):

  ```
  cd sql-scripts
  $ mysql < baseball.sql -u root -p -h 0.0.0.0 -P 3306
  ```

### Step 6 - Report: Painting count by city

  - Create a new endpoint `GET /art/reports/countbycity` that provides a count of all the paintings by city.  
  - Create a database view that utilizes [row aggregation to count the paintings by city](http://mysql.how2js.com/12-group-by/).

    - Example of creating a simple view with the `CREATE VIEW` statement:  

    ```
    use art;

    CREATE VIEW vFoo AS
    SELECT * FROM painting;
    ```

    - After calling the `GET /art/reports/countbycity` endpoint the response body should look similar to this:

      **Example Response**

      ```
      {
        reportName: "Painting count by city"
        reportData: [
          {city: "Toronto", paintingCount: 2},
          {city: "New York", paintingCount: 3},
          {city: "Paris", paintingCount: 5}
        ]
      }
      ```

  - Update your script file to reflect any new database objects you may have created to generate the report.

### Step 7 - Painting count by movement

  - Create a new endpoint `GET /art/reports/countbymovement`.  
  - Return JSON that provides a count of all the paintings by artistic movement.  Example:

  ```
  {
    reportName: "Painting count by movement"
    reportData: [
      {movement: "Impressionism", paintingCount: 2},
      {movement: "Surrealism", paintingCount: 3},
      {movement: "Post-impressionism", paintingCount: 4}
    ]
  }
  ```

  - Update your script file to reflect any new database objects.

### Step 8 - New movement table and endpoints

- Create a new table named `movement` containing a simple list of all the various artistic movements such as "post-impressionism", "impressionism", or "surrealism".  One movement can be related to many paintings.  One painting is related to one movement.
- If an attempt is made to delete a movement, do not allow any related paintings to be deleted.  Send an HTTP `409 - conflict` response code back to the user-agent (ex: POSTman).  
- Before you attempt to create the foreign key on the painting table, **you will need to update the movement column values within your painting table to correspond with the primary key values within your new movement table**.
- Be sure to update the sql script file to reflect the impact of your new table:
  - `painting` table schema changes and data changes.
  - new `movement` table  data.
- Be sure to update your existing code throughout your api and dal.  Don't forget about the impact to your reporting endpoints.  
- Create the following _new_ endpoints for your api.  

  > Do not worry about supporting these endpoints with the CouchDB dal.

  **Artistic Movements**

  - CREATE: `POST /art/movements`  - Creates a new artistic movement such as 'impressionism'.
  - READ: `GET /art/movements/:id`  - Retrieves a single artistic movement such as 'impressionism'.
  - UDPATE: `PUT /art/movements/:id` - Updates a single artistic movement
  - DELETE: `DELETE /art/movements/:id` - Deletes a single artistic movement

    >  Do not allow the deletion of a movement if the painting table contains corresponding record.  Send an 409 HTTP response with a message stating corresponding painting data exists for the movement you are attempting to delete.  Also include instructions on how to resolve the issue by removing all paintings for the given movement.  A '409 Conflict' should be used when the request could not be completed due to a conflict with the current state of the resource. This code is only allowed in situations where it is expected that the user might be able to resolve the conflict and resubmit the request. The response body SHOULD include enough information for the user to recognize the source of the conflict. Ideally, the response entity would include enough information for the user or user agent (such as POSTman) to fix the problem.  See [http://www.restapitutorial.com/httpstatuscodes.html](http://www.restapitutorial.com/httpstatuscodes.html).  

  - LIST:  `GET /art/movements` - Lists all the artistic movements.  _Don't worry about a providing a limit, filter, or pagination for the list of movements._
