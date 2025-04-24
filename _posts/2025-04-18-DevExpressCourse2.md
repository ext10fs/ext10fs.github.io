---
layout: single
title: "DevExpress Course 2"
categories: WinForm
tags: [Csharp, WinForm, DevExpress]
---

### 1. <a href="https://www.youtube.com/watch?v=-9pcFJ79zV8&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=20" target="_blank">LoginForm</a>

- LoginForm 속성 : StartPosition = CenterScreen
- textUserName 속성 : EditValue = "username"
- textPasword 속성 : EditValue = "Password", UseSystemPasswordChar = true
- label 속성 : TextAlignment = MiddleCenter

```csharp
public partial class LoginForm : DevExpress.XtraEditors.XtraForm
{
    private void LoginButton_Click(object sender, EventArgs e)
    {

        if (string.IsNullOrWhiteSpace(textUserName.Text) || string.IsNullOrWhiteSpace(textPassword.Text))
        {
            MessageBox.Show("Plz Enter UserName and Password");
            return;
        }

        DataTable dt = AdminDAL.LoginData(textUserName.Text, textPassword.Text);
        if (dt?.Rows.Count == 0)
        {
            MessageBox.Show("Bad UserName or Password");
            return;
        }
          
        DashboardForm dashboard = new DashboardForm();
        dashboard.FormClosed += (s, args) => Application.Exit();
        dashboard.Show();
        this.Hide();
    }
}
```

### 2. <a href="https://www.youtube.com/watch?v=1-_RwlU4QF0&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=21" target="_blank">Table 만들기</a>

- id : primary key 설정, 속성(ID사양 -> true)
- createTime : 속성(기본값 또는 바인딩 -> getdate())
- 테이블 스키마 설정후 crtl + s -> 테이블 이름 입력

### 3. <a href="https://www.youtube.com/watch?v=9fPcU5DVWxY&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=22" target="_blank">SQL Server Object Explorer</a>

- SQL Server Object Explorer -> Add New Connection
  - Local -> 서버이름(컴퓨터이름) -> 인증(SQL Server 인증) -> 사용자이름, 비밀번호, 데이터베이스이름
  - 속성 : 연결 문자열 복사

- DAL : Csharp Class 추가
  - DBConnection.cs

```csharp
class DBConnection
{
    public static string DbConn = "Server=localhost;Database=test;User Id = ilyoung; Password=1234;";
}
```

### 4. <a href="https://www.youtube.com/watch?v=f_1xP7Q5etU&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=23" target="_blank">DashboardForm</a>

- 아이콘 사이트 : <https://icons8.kr>
- DashboardForm
  - 속성 : isMdiContainer = true
  - 도구상자 > documentManager 속성 : MdiParent = DashboardForm

```csharp
public partial class DashboardForm : DevExpress.XtraBars.Ribbon.RibbonForm
{
    private void barButtonAdmin_ItemClick(object sender, ItemClickEventArgs e)
    {
        AdminForm adminForm = new AdminForm();
        adminForm.MdiParent = this;
        adminForm.Show();
    }

    private void barButtonStudent_ItemClick(object sender, ItemClickEventArgs e)
    {
        StudentForm studentForm = new StudentForm();
        studentForm.MdiParent = this;
        studentForm.Show();
    }

    private void barButtonTheme_ItemClick(object sender, ItemClickEventArgs e)
    {
        ThemeForm themeForm = new ThemeForm();
        themeForm.MdiParent = this; // MDI 부모 설정
        themeForm.Show();
    }

    private void barButtonAbout_ItemClick(object sender, ItemClickEventArgs e)
    {
        AboutForm aboutForm = new AboutForm();
        aboutForm.MdiParent = this;
        aboutForm.Show();
    }
}
```

### 5. <a href="https://www.youtube.com/watch?v=0Oo1PKywEFg&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=25" target="_blank">GUI</a>

- MDI Child Form : root 경로에 생성한 뒤 GUI 폴더로 옮김 (폴더에서 생성하면 namespace StudentRegistration.GUI)
  - AdminForm : 관리자 정보
  - StudentForm : 학생 정보
  - ThemeForm : 테마 설정
  - AboutForm : 프로그램 정보

```csharp
using DevExpress.Skins; // for SkinManager

public partial class ThemeForm : DevExpress.XtraEditors.XtraForm
{
    private void ThemeForm_Load(object sender, EventArgs e)
    {
        foreach (SkinContainer sc in SkinManager.Default.Skins)
        {
            comboBoxSkin.Properties.Items.Add(sc.SkinName);
        }
    }

    private void comboBoxSkin_SelectedIndexChanged(object sender, EventArgs e)
    {
        DevExpress.LookAndFeel.UserLookAndFeel.Default.SetSkinStyle(comboBoxSkin.Text);
    }
}
```

### 6. <a href="https://www.youtube.com/watch?v=VyNvMxtJN_k&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=26" target="_blank">Project Architecture</a>

- Project Architecture
  - Data Access Layer : DB 연결 및 CRUD 처리 (DAL)
  - Business Logic Layer : 비즈니스 로직 처리 (BLL)
  - Presentation Layer : UI 처리 (GUI)

- BLL : Csharp Class 추가
  - StudentBLL.cs

```csharp
class StudentBLL
{
    public long Id { get; set; }
    public String Name { get; set; }
    public String Phone { get; set; }
    public String Blood { get; set; }
    public String Gender { get; set; }
    public String Email { get; set; }
    public String Dob { get; set; }
    public String Description { get; set; }
}
```

### 7. <a href="https://www.youtube.com/watch?v=i2DhxdYIlZ4&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=27" target="_blank">DAL</a>

- DAL : Csharp Class 추가
  - StudentDAL.cs

```csharp
 static class StudentDAL
{
    static readonly SqlConnection conn = new SqlConnection(DBConnection.DbConn);

    #region SelectData
    public static DataTable SelectData()
    {
        DataTable dt = new DataTable();
        try
        {
            string sql = "SELECT * FROM student";
            SqlCommand sqlCmd = new SqlCommand(sql, conn);
            SqlDataAdapter adapter = new SqlDataAdapter(sqlCmd);
            conn.Open();
            adapter.Fill(dt);
        }
        catch (Exception ex)
        {
            MessageBox.Show(ex.Message);
        }
        finally
        {
            conn.Close();
        }
        return dt;
    }
    #endregion
    
    #region InsertData
    public static bool InsertData(StudentBLL student)
    {
        try
        {
            string sql = "INSERT INTO student(name,phone,blood,gender,email,dob,description) values(@name,@phone,@blood,@gender,@email,@dob,@description)";
            SqlCommand sqlCmd = new SqlCommand(sql, conn);
            sqlCmd.Parameters.AddWithValue("@name", student.Name);
            sqlCmd.Parameters.AddWithValue("@phone", student.Phone);
            sqlCmd.Parameters.AddWithValue("@blood", student.Blood);
            sqlCmd.Parameters.AddWithValue("@gender", student.Gender);
            sqlCmd.Parameters.AddWithValue("@email", student.Email);
            sqlCmd.Parameters.AddWithValue("@dob", student.Dob);
            sqlCmd.Parameters.AddWithValue("@description", student.Description);
            conn.Open();
            sqlCmd.ExecuteNonQuery();
        }
        catch (Exception ex)
        {
            MessageBox.Show(ex.Message);
            return false;
        }
        finally
        {
            conn.Close();
        }
        return true;
    }
    #endregion

    #region UpdateData
    public static bool UpdateData(StudentBLL student)
    {
        try
        {
            string sql = "UPDATE student SET name=@name,phone=@phone,blood=@blood,gender=@gender,email=@email,dob=@dob,description=@description WHERE id=@id";
            SqlCommand sqlCmd = new SqlCommand(sql, conn);
            sqlCmd.Parameters.AddWithValue("@id", student.Id);
            sqlCmd.Parameters.AddWithValue("@name", student.Name);
            sqlCmd.Parameters.AddWithValue("@phone", student.Phone);
            sqlCmd.Parameters.AddWithValue("@blood", student.Blood);
            sqlCmd.Parameters.AddWithValue("@gender", student.Gender);
            sqlCmd.Parameters.AddWithValue("@email", student.Email);
            sqlCmd.Parameters.AddWithValue("@dob", student.Dob);
            sqlCmd.Parameters.AddWithValue("@description", student.Description);
            conn.Open();
            sqlCmd.ExecuteNonQuery();
        }
        catch (Exception ex)
        {
            MessageBox.Show(ex.Message);
            return false;
        }
        finally
        {
            conn.Close();
        }
        return true;
    }
    #endregion
}
```

### 8. <a href="https://www.youtube.com/watch?v=Oj3P-Xh4igQ&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=28" target="_blank">StudentForm</a>

- StudentForm
  - XtraTabControl
  - LayoutControl
  - GridControl
  - TextEdit
  - ComboBoxEdit : Edit items
  - DateEdit

### 9. <a href="https://www.youtube.com/watch?v=7zy92UQ4VUg&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=29" target="_blank">AdminDAL</a>

- DAL : Csharp Class 추가
  - AdminDAL.cs

```csharp
static class AdminDAL
{
    private static readonly string connectionString = DBConnection.DbConn;

    #region LoginData
    public static DataTable LoginData(string userName, string password)
    {
        DataTable dt = new DataTable();
        
        using (var conn = new SqlConnection(connectionString))
        using (var sqlCmd = new SqlCommand("SELECT * FROM admin WHERE userName=@userName and password=@password", conn))
        {
            try
            {
                sqlCmd.Parameters.AddWithValue("@userName", userName);
                sqlCmd.Parameters.AddWithValue("@password", password);
                
                using (var adapter = new SqlDataAdapter(sqlCmd))
                {
                    conn.Open();
                    adapter.Fill(dt);
                }
            }
            catch (SqlException ex)
            {
                MessageBox.Show($"데이터베이스 오류: {ex.Message}");
            }
        }
        return dt;
    }
    #endregion

}
```

### 10. <a href="https://www.youtube.com/watch?v=xp45I0w79b0&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=30" target="_blank">StudentForm</a>

- GridVewStudent
  - property : editable = false
  - event : load, doubleClick

```csharp
public partial class StudentForm : DevExpress.XtraEditors.XtraForm
{
    public StudentForm()
    {
        InitializeComponent();
    }

    #region Event
    private void StudentForm_Load(object sender, EventArgs e)
    {
        StudentListGridControl.DataSource = StudentDAL.SelectData();
    }

    private void GridViewStudent_DoubleClick(object sender, EventArgs e)
    {
        DataRow row = GridViewStudent.GetDataRow(GridViewStudent.GetSelectedRows()[0]);

        if (row == null || string.IsNullOrWhiteSpace(row[0].ToString()))
        {
            return;   
        }

        IdTextEdit.Text = row[0].ToString();
        NameTextEdit.Text = row[1].ToString();
        PhoneTextEdit.Text = row[2].ToString();
        BloodComboBoxEdit.Text = row[3].ToString();
        GenderComboBoxEdit.Text = row[4].ToString();
        EmailTextEdit.Text = row[5].ToString();
        DescriptionTextEdit.Text = row[7].ToString();
        DOBDateEdit.Text = row[6].ToString();
    }
    #endregion

    #region ButtonEvent
    private void ButtonPrint_Click(object sender, EventArgs e)
    {
        GridViewStudent.ShowPrintPreview();
    }

    private void ClearText()
    {
        IdTextEdit.Text = "";
        NameTextEdit.Text = "";
        PhoneTextEdit.Text = "";
        BloodComboBoxEdit.Text = "";
        GenderComboBoxEdit.Text = "";
        EmailTextEdit.Text = "";
        DescriptionTextEdit.Text = "";
        DOBDateEdit.Text = "";
    }

    private void AddButton_Click(object sender, EventArgs e)
    {
        if (string.IsNullOrWhiteSpace(NameTextEdit.Text) 
            || string.IsNullOrWhiteSpace(PhoneTextEdit.Text)
            || string.IsNullOrWhiteSpace(BloodComboBoxEdit.Text)
            || string.IsNullOrWhiteSpace(GenderComboBoxEdit.Text)
            || string.IsNullOrWhiteSpace(EmailTextEdit.Text)
            || string.IsNullOrWhiteSpace(DescriptionTextEdit.Text)
            || string.IsNullOrWhiteSpace(DOBDateEdit.Text))
        {
            MessageBox.Show("Plz Enter Information");
            return;
        }

        StudentBLL student = new StudentBLL();
        student.Name = NameTextEdit.Text;
        student.Phone = PhoneTextEdit.Text;
        student.Blood = BloodComboBoxEdit.Text;
        student.Gender = GenderComboBoxEdit.Text;
        student.Email = EmailTextEdit.Text;
        student.Description = DescriptionTextEdit.Text;
        student.Dob = DOBDateEdit.Text;

        if (StudentDAL.InsertData(student) == false)
        {
            MessageBox.Show("Data Add Fail");
            return;
        }
        StudentListGridControl.DataSource = StudentDAL.SelectData();
        ClearText();
    }

    private void ClearButton_Click(object sender, EventArgs e)
    {
        ClearText();
    }

    private void UpdateButton_Click(object sender, EventArgs e)
    {
        if (string.IsNullOrWhiteSpace(IdTextEdit.Text) 
            || string.IsNullOrWhiteSpace(NameTextEdit.Text)
            || string.IsNullOrWhiteSpace(PhoneTextEdit.Text)
            || string.IsNullOrWhiteSpace(BloodComboBoxEdit.Text)
            || string.IsNullOrWhiteSpace(GenderComboBoxEdit.Text)
            || string.IsNullOrWhiteSpace(EmailTextEdit.Text)
            || string.IsNullOrWhiteSpace(DescriptionTextEdit.Text)
            || string.IsNullOrWhiteSpace(DOBDateEdit.Text))
        {
            MessageBox.Show("Plz Enter Information");
            return;
        }

        StudentBLL student = new StudentBLL();
        student.Id = long.Parse(IdTextEdit.Text);  
        student.Name = NameTextEdit.Text;
        student.Phone = PhoneTextEdit.Text;
        student.Blood = BloodComboBoxEdit.Text;
        student.Gender = GenderComboBoxEdit.Text;
        student.Email = EmailTextEdit.Text;
        student.Description = DescriptionTextEdit.Text;
        student.Dob = DOBDateEdit.Text;
        
        if (StudentDAL.UpdateData(student) == false)
        {
            MessageBox.Show("Data Add Fail");
            return;
        }
        StudentListGridControl.DataSource = StudentDAL.SelectData();
        ClearText();
    }
    #endregion
}
```
