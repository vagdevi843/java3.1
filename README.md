# java3.1
import java.io.*;
import java.sql.*;
import jakarta.servlet.*;
import jakarta.servlet.http.*;

public class UserServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        String username = request.getParameter("username");
        String password = request.getParameter("password");

        try {
            // Load JDBC driver
            Class.forName("com.mysql.cj.jdbc.Driver");
            Connection conn = DriverManager.getConnection(
                "jdbc:mysql://localhost:3306/yourDB", "root", "password");

            // Validate user credentials from a Users table
            PreparedStatement ps = conn.prepareStatement(
                "SELECT * FROM Users WHERE username=? AND password=?");
            ps.setString(1, username);
            ps.setString(2, password);
            ResultSet rs = ps.executeQuery();

            if (rs.next()) {
                out.println("<h2>Welcome, " + username + "!</h2>");

                // Display all employees as a table
                Statement stmt = conn.createStatement();
                ResultSet empRs = stmt.executeQuery("SELECT * FROM Employee");

                out.println("<h3>Employee Records:</h3>");
                out.println("<table border='1'><tr><th>EmpID</th><th>Name</th><th>Salary</th></tr>");
                while (empRs.next()) {
                    out.println("<tr><td>" + empRs.getInt("EmpID") + "</td>"
                            + "<td>" + empRs.getString("Name") + "</td>"
                            + "<td>" + empRs.getDouble("Salary") + "</td></tr>");
                }
                out.println("</table>");
            } else {
                out.println("<h2>Invalid username or password!</h2>");
            }

            conn.close();
        } catch (Exception e) {
            out.println("Error: " + e.getMessage());
        }
    }
}
