# 使用Java的gui与数据库完成数据库查看表的数据
```java
package factory;

import java.awt.*;
import java.awt.event.*;
import javax.swing.*;
import java.sql.*;

public class Gui extends JFrame {
    private JComboBox<String> databaseBox;
    private JTextArea queryArea;
    private JTextArea resultArea;
    private Connection connection;
    
    public Gui() {
        setLayout(new BorderLayout());
        
        databaseBox = new JComboBox<String>();
        queryArea = new JTextArea();
        resultArea = new JTextArea();
        JButton executeButton = new JButton("Execute");
        
        add(databaseBox, BorderLayout.NORTH);
        add(new JScrollPane(queryArea), BorderLayout.CENTER);
        add(new JScrollPane(resultArea), BorderLayout.SOUTH);
        add(executeButton, BorderLayout.EAST);
        
        executeButton.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e) {
                executeQuery();
            }
        });
        
        try {
            connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/factory", "root", "771028.Zz");
            Statement statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery("SHOW DATABASES");
            while(resultSet.next()) {
                databaseBox.addItem(resultSet.getString(1));
            }
            resultSet.close();
            statement.close();
        } catch (SQLException e) {
            e.printStackTrace();
            JOptionPane.showMessageDialog(this, "Database connection failed: " + e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
        
        setSize(800, 600);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setVisible(true);
    }
    
    private void executeQuery() {
        String database = (String) databaseBox.getSelectedItem();
        String query = queryArea.getText();
        resultArea.setText(""); // Clear previous results
        
        try {
            connection.setCatalog(database);
            Statement statement = connection.createStatement();
            ResultSet resultSet = statement.executeQuery(query);
            ResultSetMetaData metaData = resultSet.getMetaData();
            int columnCount = metaData.getColumnCount();
            
            // Display column names
            StringBuilder resultText = new StringBuilder();
            for (int i = 1; i <= columnCount; i++) {
                resultText.append(metaData.getColumnName(i)).append("\t");
            }
            resultText.append("\n");
            
            // Display rows
            while (resultSet.next()) {
                for (int i = 1; i <= columnCount; i++) {
                    resultText.append(resultSet.getString(i)).append("\t");
                }
                resultText.append("\n");
            }
            
            resultArea.setText(resultText.toString());
            
            resultSet.close();
            statement.close();
        } catch (SQLException e) {
            e.printStackTrace();
            JOptionPane.showMessageDialog(this, "Query execution failed: " + e.getMessage(), "Error", JOptionPane.ERROR_MESSAGE);
        }
    }
    
    public static void main(String[] args) {
        new Gui();
    }
}
```
主要Java代码

```
CREATE TABLE s (
    sno char(2),
    sname varchar(45),
    stat  int,
    city varchar(45),
    PRIMARY KEY(sno)
);

CREATE TABLE p (
    pno char(2),
    pname varchar(45),
    color varchar(45),
    weight int,
    PRIMARY KEY(pno)
);

CREATE TABLE j (
    jno char(2),
    jname varchar(45),
    city varchar(45),
    PRIMARY KEY(jno)
);

CREATE TABLE spj (
    sno char(2),
    pno char(2),
    jno char(2),
    qty varchar(45),
    PRIMARY KEY(sno, pno, jno),
    FOREIGN KEY (sno) REFERENCES s(sno),
    FOREIGN KEY (pno) REFERENCES p(pno),
    FOREIGN KEY (jno) REFERENCES j(jno)
);

2、写出把上述表中数据输入到对应表中的过程（要求使用SQL语句完成）。
INSERT INTO `factory`.`s` (`SNO`, `Sname`, `stat`, `city`) VALUES ('s1', '精益', '20', '天津');
INSERT INTO `factory`.`s` (`SNO`, `Sname`, `stat`, `city`) VALUES ('s2', '盛锡', '10', '北京');
INSERT INTO `factory`.`s` (`SNO`, `Sname`, `stat`, `city`) VALUES ('s3', '东方红', '30', '北京');
INSERT INTO `factory`.`s` (`SNO`, `Sname`, `stat`, `city`) VALUES ('s4', '丰泰盛', '20', '天津');
INSERT INTO `factory`.`s` (`SNO`, `Sname`, `stat`, `city`) VALUES ('s5', '为民', '30', '上海');

INSERT INTO `factory`.`p` (`pno`, `pname`, `color`, `weight`) VALUES ('p1', '螺母', '红', '12');
INSERT INTO `factory`.`p` (`pno`, `pname`, `color`, `weight`) VALUES ('p2', '螺栓', '绿', '17');
INSERT INTO `factory`.`p` (`pno`, `pname`, `color`, `weight`) VALUES ('p3', '螺丝刀', '蓝', '14');
INSERT INTO `factory`.`p` (`pno`, `pname`, `color`, `weight`) VALUES ('p4', '螺丝刀', '红', '14');
INSERT INTO `factory`.`p` (`pno`, `pname`, `color`, `weight`) VALUES ('p5', '凸轮', '蓝', '40');
INSERT INTO `factory`.`p` (`pno`, `pname`, `color`, `weight`) VALUES ('p6', '齿轮', '红', '30');

INSERT INTO `factory`.`j` (`jno`, `jname`, `city`) VALUES ('j1', '三建', '北京');
INSERT INTO `factory`.`j` (`jno`, `jname`, `city`) VALUES ('j2', '一汽', '长春');
INSERT INTO `factory`.`j` (`jno`, `jname`, `city`) VALUES ('j3', '弹簧厂', '天津');
INSERT INTO `factory`.`j` (`jno`, `jname`, `city`) VALUES ('j4', '造船厂', '天津');
INSERT INTO `factory`.`j` (`jno`, `jname`, `city`) VALUES ('j5', '机车厂', '唐山');
INSERT INTO `factory`.`j` (`jno`, `jname`, `city`) VALUES ('j6', '无线电厂', '常州');
INSERT INTO `factory`.`j` (`jno`, `jname`, `city`) VALUES ('j7', '半导体厂', '南京');

INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s1', 'p1', 'j1', '200');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s1', 'p1', 'j3', '100');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s1', 'p1', 'j4', '700');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s1', 'p2', 'j2', '100');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('S2', 'p3', 'j1', '400');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('S2', 'p3', 'j2', '200');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('S2', 'p3', 'j4', '500');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('S2', 'p3', 'j5', '400');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('S2', 'p5', 'j1', '400');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('S2', 'p5', 'j2', '100');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s3', 'p1', 'j1', '200');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s3', 'p3', 'j1', '200');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s4', 'p5', 'j1', '100');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s4', 'p6', 'j3', '300');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s4', 'p6', 'j4', '200');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s5', 'p2', 'j4', '100');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s5', 'p3', 'j1', '200');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s5', 'p6', 'j2', '200');
INSERT INTO `factory`.`spj` (`sno`, `pno`, `jno`, `qty`) VALUES ('s5', 'p6', 'j4', '500');
```
数据库代码
简单使用gui将数据库与Java相联系，完成了可视化的查询