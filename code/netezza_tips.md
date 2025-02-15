## Netezza Tips and Tricks ##
### Netezza Books & Articles ###
- [Netezza Database User Guide](https://github.com/Avkash/mldl/blob/master/pages/docs/books/NetezzaDatabaseUserGuilde.pdf)
- [Netezza System Administration Guide](https://github.com/Avkash/mldl/blob/master/pages/docs/books/NetezzaSystemAdministratorGuide.pdf)

### Netezza Simulator VM ###
 - The VM I Used is > NetezzaSoftwareEmulator_7.2.1.ova
 - Base login >> root/netezza (This is the base VM access as root)
 - Please use username “nz” and password “nz” to login directly to netezza instance
 
### CLI  ###

This is how you can connect to Netezza database fron CLI
```
$ nzsql -u admin -pw password
SYSTEM.ADMIN(ADMIN)=>
```
This is how you can connect to Netezza database fron CLI
```
$ nzsql -u avkash -pw password -d _database_name_to_create_

Once you are logged into Netezza database you will see a prompt as below:
DBNAME.ADMIN(ADMIN)=>
```
To change databsse you can run the command as below:
```
=> set catalog <db_name>;
```
To get a list all tables run following query:
```
=> SELECT TABLENAME,OWNER,CREATEDATE FROM _V_TABLE WHERE OBJTYPE='TABLE';
```
This is how you will create a new table:
```
=> CREATE TABLE allusers (id int, age int, name char(20));
```
This is how you will enter a record into table:
```
=> INSERT INTO allusers (id, age, name) values (1, 20, 'Jim');
```
This is how you will list all records from a table
```
=> select * from allusers;
=> select * from allusers limit 1;
```


### Connecting Netezza server from Python Sample ###

Check out my [Ipython Jupyter Notebook with Python Sample](https://github.com/Avkash/mldl/blob/master/notebook/utils/Netezza-Connection-Demo.ipynb)

Step 1: Importing python jaydebeapi library
```
import jaydebeapi
```
Step 2: Setting Database connection settings
```
dsn_database = "avkash"            
dsn_hostname = "172.16.181.131" 
dsn_port = "5480"                
dsn_uid = "admin"        
dsn_pwd = "password"      
jdbc_driver_name = "org.netezza.Driver"
jdbc_driver_loc = "/Users/avkashchauhan/learn/customers/netezza/nzjdbc3.jar"
###jdbc:netezza://" + server + "/" + dbName ;
connection_string='jdbc:netezza://'+dsn_hostname+':'+dsn_port+'/'+dsn_database
url = '{0}:user={1};password={2}'.format(connection_string, dsn_uid, dsn_pwd)
print("URL: " + url)
print("Connection String: " + connection_string)
```
Step 3:Creating Database Connection
```
conn = jaydebeapi.connect("org.netezza.Driver", connection_string, {'user': dsn_uid, 'password': dsn_pwd},
                         jars = "/Users/avkashchauhan/learn/customers/netezza/nzjdbc3.jar")
curs = conn.cursor()
```
Step 4:Processing SQL Query
```
curs.execute("select * from allusers")
result = curs.fetchall()
print("Total records: " + str(len(result)))
print(result[0])
```
Step 5: Printing all records
```
for i in range(len(result)):
    print(result[i])
```
Step 6: Closing all connections
```
curs.close()
conn.close()
```

### Connecting Netezza server from Java Code Sample ###

Step 1: Have the Netezza driver as nzjdbc3.jar in a folder.

Step 2: Create netezzaJdbcMain.java as below in the same folder where nzjdbc3.jar is placed.

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
public class netezzaJdbcMain {
    public static void main(String[] args) {
        String server = "x.x.x.x";
        String port = "5480";
        String dbName = "_db_name_";
        String url = "jdbc:netezza://" + server + "/" + dbName ;
        String user = "admin";
        String pwd = "password";
        String schema = "db_schema";
        Connection conn = null;
        Statement st = null;
        ResultSet rs = null;
        try {
            Class.forName("org.netezza.Driver");
            System.out.println(" Connecting ... ");
            conn = DriverManager.getConnection(url, user, pwd);
            System.out.println(" Connected "+conn);
            
            String sql = "select * from allusers";
            st = conn.createStatement();
            rs = st.executeQuery(sql);

            System.out.println("Printing result...");
            int i = 0;
            while (rs.next()) {
                String userName = rs.getString("name");
                int year = rs.getInt("age");
                System.out.println("User: " + userName +
                        ", age is: " + year);
                i++;
            }
            if (i==0){
                System.out.println(" No data found");
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if( rs != null) 
                    rs.close();
                if( st!= null)
                    st.close();
                if( conn != null)
                    conn.close();
            } catch (SQLException e1) {
                    e1.printStackTrace();
                }
        }
    }
}
```
Step 3: Compile code as below:
```
$ javac -cp nzjdbc3.jar -J-Xmx2g -J-XX:MaxPermSize=128m netezzaJdbcMin.java                                                                                                                                
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=128m; support was removed in 8.0
```
Note: You should see your main class is compiled without any problem.

Step 4: Run compiled class as below:

```
$ java -cp .:nzjdbc3.jar netezzaJdbcMain

 Connecting ...
 Connected org.netezza.sql.NzConnection@3feba861
Printing result...
User: John                , age is: 30
User: Jason               , age is: 26
User: Jim                 , age is: 20
User: Kyle                , age is: 21
User: Kim                 , age is: 27

```
Note: You will see results something as above. 
