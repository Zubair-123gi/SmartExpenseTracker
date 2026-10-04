import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.io.*;
public class SmartExpenseTracker extends JFrame {
    String[] categories = new String[100];
    double[] amounts = new double[100];
    String[] dates = new String[100];
    int expenseCount = 0;
    double monthlyBudget = 0;
    CardLayout card = new CardLayout();
    JPanel mainPanel = new JPanel(card);
    JTextField userField;
    JPasswordField passField;
    JTextField txtAmount;
    JTextField txtDate;
    JTextField txtBudget;
    JTextField txtFilter;
    JComboBox<String> cmbCategory;
    JTable table;
    DefaultTableModel model;
    JLabel totalLabel;
    JLabel budgetLabel;
    public SmartExpenseTracker() {
        setTitle("Smart Expense Tracker");
        setSize(1919,1080);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        loginPage();
        trackerPage();
        add(mainPanel);
        card.show(mainPanel,"LOGIN");
        setVisible(true);
    }
    void loginPage(){
        JPanel panel =
                new JPanel(new GridBagLayout());
        GridBagConstraints g =
                new GridBagConstraints();
        g.insets =
                new Insets(10,10,10,10);
        JLabel title =
                new JLabel("Smart Expense Tracker");
        title.setFont(
                new Font("Arial",
                        Font.BOLD,20));
        userField =
                new JTextField(15);
        passField =
                new JPasswordField(15);
        JButton login =
                new JButton("Sign In");
        g.gridx=0;
        g.gridy=0;
        g.gridwidth=2;
        panel.add(title,g);
        g.gridwidth=1;
        g.gridx=0;
        g.gridy=1;
        panel.add(
                new JLabel("Username"),
                g);
        g.gridx=1;
        panel.add(userField,g);
        g.gridx=0;
        g.gridy=2;
        panel.add(
                new JLabel("Password"),
                g);
        g.gridx=1;
        panel.add(passField,g);
        g.gridx=0;
        g.gridy=3;
        g.gridwidth=2;
        panel.add(login,g);
        login.addActionListener(e->{
            String u =
                    userField.getText();
            String p =
                    String.valueOf(
                            passField.getPassword());
            if(u.equals("admin")
                    && p.equals("1234")){
                card.show(
                        mainPanel,
                        "TRACKER");
            }
            else{
                JOptionPane.showMessageDialog(
                        this,
                        "Invalid Login");
            }
        });
        mainPanel.add(panel,"LOGIN");
    }
    void trackerPage(){
        JPanel tracker =
                new JPanel(new BorderLayout());
        JPanel top =
                new JPanel(
                        new GridLayout(5,2,5,5));
        top.add(new JLabel("Amount"));
        txtAmount =
                new JTextField();
        top.add(txtAmount);
        top.add(new JLabel("Category"));
        cmbCategory =
                new JComboBox<>(
                        new String[]{
                                "Food",
                                "Travel",
                                "Shopping",
                                "Bills",
                                "Education",
                                "Other"
                        });
        top.add(cmbCategory);
        top.add(new JLabel("Date"));
        txtDate =
                new JTextField();
        top.add(txtDate);
        top.add(new JLabel("Budget"));
        txtBudget =
                new JTextField();
        top.add(txtBudget);
        JButton set =
                new JButton("Set Budget");
        JButton add =
                new JButton("Add Expense");
        top.add(set);
        top.add(add);
        tracker.add(
                top,
                BorderLayout.NORTH);
        model =
                new DefaultTableModel();
        model.addColumn("Amount");
        model.addColumn("Category");
        model.addColumn("Date");
        table =
                new JTable(model);
        tracker.add(
                new JScrollPane(table),
                BorderLayout.CENTER);
        JPanel bottom =
                new JPanel();
        JButton delete =
                new JButton("Delete");
        JButton save =
                new JButton("Save");
        JButton load =
                new JButton("Load");
        JButton filter =
                new JButton("Filter");
        JButton all =
                new JButton("Show All");
        JButton logout =
                new JButton("Logout");
        txtFilter =
                new JTextField(8);
        totalLabel =
                new JLabel("Total: 0");
        budgetLabel =
                new JLabel("Remaining: 0");
        bottom.add(delete);
        bottom.add(save);
        bottom.add(load);
        bottom.add(txtFilter);
        bottom.add(filter);
        bottom.add(all);
        bottom.add(totalLabel);
        bottom.add(budgetLabel);
        bottom.add(logout);
        tracker.add(
                bottom,
                BorderLayout.SOUTH);
        set.addActionListener(e->setBudget());
        add.addActionListener(e->addExpense());
        delete.addActionListener(e->delete());
        save.addActionListener(e->save());
        load.addActionListener(e->load());
        filter.addActionListener(
                e->filter(txtFilter.getText()));
        all.addActionListener(
                e->refresh());
        logout.addActionListener(
                e->card.show(mainPanel,"LOGIN"));
        mainPanel.add(
                tracker,
                "TRACKER");
    }
    void setBudget(){
        try{
            monthlyBudget =
                    Double.parseDouble(
                            txtBudget.getText());
            update();
        }
        catch(Exception e){
            JOptionPane.showMessageDialog(
                    this,
                    "Invalid Budget");
        }
    }
    void addExpense(){
        try{
            double a =
                    Double.parseDouble(
                            txtAmount.getText());
            amounts[expenseCount]=a;
            categories[expenseCount]=
                    cmbCategory.getSelectedItem()
                            .toString();
            dates[expenseCount]=
                    txtDate.getText();
            expenseCount++;
            refresh();
            update();
        }
        catch(Exception e){
            JOptionPane.showMessageDialog(
                    this,
                    "Invalid Amount");
        }
    }
    double total(){
        double t=0;
        for(int i=0;i<expenseCount;i++)
            t+=amounts[i];
        return t;
    }
    void update(){
        totalLabel.setText(
                "Total: Rs."+total());
        budgetLabel.setText(
                "Remaining: Rs."
                        +(monthlyBudget-total()));
        if(monthlyBudget>0 &&
                total()>=monthlyBudget*0.8){
            JOptionPane.showMessageDialog(
                    this,
                    "Budget Warning! more than 80% of budget has been used");
        }
    }
    void delete(){
        int r =
                table.getSelectedRow();
        if(r==-1)
            return;
        for(int i=r;i<expenseCount-1;i++){
            amounts[i]=amounts[i+1];
            categories[i]=categories[i+1];
            dates[i]=dates[i+1];
        }
        expenseCount--;
        refresh();
        update();
    }
    void refresh(){
        model.setRowCount(0);
        for(int i=0;i<expenseCount;i++)
            model.addRow(
                    new Object[]{
                            amounts[i],
                            categories[i],
                            dates[i]
                    });
    }
    void filter(String c){
        model.setRowCount(0);
        for(int i=0;i<expenseCount;i++)
            if(categories[i]
                    .equalsIgnoreCase(c))
                model.addRow(
                        new Object[]{
                                amounts[i],
                                categories[i],
                                dates[i]
                        });

    }
    void save(){
        try{
            PrintWriter pw =
                    new PrintWriter(
                            "expense.txt");
            for(int i=0;i<expenseCount;i++)
                pw.println(
                        amounts[i]+","+
                                categories[i]+","+
                                dates[i]);
            pw.close();
            JOptionPane.showMessageDialog(
                    this,
                    "Saved");
        }
        catch(IOException e){
            JOptionPane.showMessageDialog(
                    this,
                    "Save Error");

        }
    }
    void load(){
        try{
            BufferedReader br =
                    new BufferedReader(
                            new FileReader(
                                    "expense.txt"));
            expenseCount=0;
            String line;
            while((line=br.readLine())!=null){
                String d[] =
                        line.split(",");
                amounts[expenseCount]=
                        Double.parseDouble(d[0]);
                categories[expenseCount]=d[1];
                dates[expenseCount]=d[2];
                expenseCount++;
            }
            br.close();
            refresh();
            update();
        }
        catch(Exception e){
            JOptionPane.showMessageDialog(
                    this,
                    "Load Error");
        }
    }
    public static void main(String[] args){
        SwingUtilities.invokeLater(
                SmartExpenseTracker::new);
    }
}