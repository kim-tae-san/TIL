<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ page import="java.sql.*"%>
<html>
        <head><title>hello world</title></head>
        <body>
                tomcat test (was2)
                DB Connected!!
                now_time : <%= new java.util.Date()%>

<%
        try{
                String url = "jdbc:postgresql://postgres.csb3zxp87qsw.ap-northeast-2.rds.amazonaws.com:5432/tech_project";
                String usr = "postgres";
                String pwd = "rlaxo2135";
                Class.forName("org.postgresql.Driver");
                out.println("hello");
                Connection db = DriverManager.getConnection(url,usr,pwd);
                Statement st = db.createStatement();
                out.println("START!!!!!!!");

                //String create_data = "create table customer (id int NOT NULL, name varchar(20) NOT NULL, age int NOT NULL, PRIMARY KEY (id));";
                //st.executeUpdate(create_data);


                //out.println("table ho");
                //String insert_data = "insert into customer values (1, 'taesan', 25);" + "insert into customer values (2, 'kimtae', 26);";

                //st.executeUpdate(insert_data);
                //out.println("data ho!");
                String select_query = "select * from customer";

                ResultSet rs = st.executeQuery(select_query);
                %>
                <table border="1" cellspacing="0">
                <tr>
                        <td>id</td>
                        <td>name</td>
                        <td>age</td>
                </tr>
                <%
                while(rs.next()){
                %><tr>
                        <td><%=rs.getInt("id")%></td>
                        <td><%=rs.getString("name")%></td>
                        <td><%=rs.getInt("age")%></td>
                </tr>
                <%
                }
                %></table>
        <%
        }
        catch(Exception e){
                e.printStackTrace();
}
%>

        </body>
</html>