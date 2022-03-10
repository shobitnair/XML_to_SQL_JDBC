# CS305 Assignment 1
### Name : Shobit Nair
### Enrollment Number : 2019CSB1121

> Main : https://github.com/shobitnair/CS305_2022
>
> Assignment_1 : https://github.com/shobitnair/CS305_2022/tree/assignment_1
# About the assignment
This assignment is about creating a JAVA Library which parses SQL commands from a XML CDATA file called *queries.xml* and then converts them into modified SQL command that can be directly fed to MySQL server. In this way we can create various template SQL commands that just needs user to input specific parameters as per the parameters needed for that query.

### File Structure of the CS305_2022/lib/src folder.

    ├───main
    │   ├───java
    │   │   └───sqlRunner
    │   │           Library.java
    │   │           queryHandler.java
    │   │           sqlRunnerInterface.java
    │   │           typeWrapper.java
    │   │
    │   └───resources
    │           queries.xml
    │
    └───test
        ├───java
        │   └───sqlRunner
        │           LibraryTest.java
        │
        └───resources


- Parsing and Query Modifications are done by queryHandler.java

- Primitive and Array Type checking done by typeWrapper.java

- All queries and Database connections are managed in Library.java. Library.java acts as the parent class
  and directly deals with all the API calls that are made from LibraryTest.java.
- LibraryTest.java manages *Various Junit testing to ensure about 96% Test coverage of the code.*


## Logic and work flow

#### Connection to MySQL is done using JDBC SQL driver library. (Library.java)

    public boolean connectDatabase(String database_URL , String User , String Password) {  
        connection = DriverManager.getConnection(database_URL, User , Password);  
        statement = connection.createStatement();  
        System.out.println("\n>> Succesfully connected to Sakila Database");
    }  



###Parsing ( Done in *QueryHandler.java* )

    import javax.xml.parsers.DocumentBuilderFactory;
    import javax.xml.parsers.DocumentBuilder;

    path = "src/main/resources/queries.xml";
    f = new File(path);
    dbF = DocumentBuilderFactory.newInstance();
    dB = dbF.newDocumentBuilder();
    doc = dB.parse(f);
    doc.getDocumentElement().normalize();


We parse SQL commands from an XML CDATA file that contains various SQL commands assigned to a specific [queryID] and a [Paramtype].

#### Sample SQL query in queries.xml file

    <sql id="selectMany_film" paramType="film">
        <![CDATA[ SELECT * FROM sakila.film WHERE rental_duration > ${rental_duration} AND rental_rate < ${rental_rate};]]>
    </sql>

This SQL command is then modified as per the class that is passed on and all ${props} attributes as changed corresponding to the same declared fields in the supplied class.






#### Film Class and Testing the above SQL query.

    public static class film{
    int film_id;
    String title;
    String description;
    int release_year;
    int language_id;
    int original_language_id;
    int rental_duration;
    double rental_rate;
    int length;
    double replacement_cost;
    String rating;
    String special_features;
    LocalDateTime last_update;
    }

#### Run the below code as a testing method for SelectMany Query.

    fobj.rental_duration = 6;
    fobj.rental_rate = 2.25;
    List<film> flist = (List<film>)sr.selectMany("selectMany_film" , fobj , film.class);
    assertEquals(flist.size() , 59);
The modified SQL command is then sent from the library to MySQL server and a result is obtained from the same.
This is obtained by replacing the respective ${film.props} with props.

    Modified SQL Query -> { SELECT * FROM sakila.film WHERE rental_duration > '6' AND rental_rate < '2.25'; }


## Testing the code locally on your system

    git clone https://github.com/shobitnair/CS305_2022
    git checkout assignment_1
    git pull
    


- Make sure you have Gradle and Java installed in your computer.
- Preferably use IntelliJ IDE for easy testing and coverage results.
- MySQL workbench should be installed and must have Sakila Database for testing.
- Change the Host , Username and Password in LibraryTest.java (setUP) method to your local system credentials.

            void setUp(){
                String DATABASE_URL = "jdbc:mysql://localhost:3306/sakila";
                String USERNAME = "Your_username";
                String PASSWORD = "Your_Pass";
                assertTrue(sr.connectDatabase( DATABASE_URL, USERNAME , PASSWORD));
            }

### JUNIT TESTING
- Use IntelliJ IDE and use LibraryTest.java file to run all the unit
  tests.
- New tests can be added by inserting a new SQL CDATA file as
  per your requirement and creating a call function in LibraryTest.java respectively.



> All corner cases and exception cases have been added for ease of testing and 96% code coverage.
> I have used sakila.film and sakila.language database for testing. The classes for the same can also be found in the LibraryTest.java
