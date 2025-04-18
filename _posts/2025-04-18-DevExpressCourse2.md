---
layout: single
title: "DevExpress Course"
categories: WinForm
tags: [Csharp, WinForm, DevExpress]
---

### 1. <a href="https://www.youtube.com/watch?v=TZjkvn1WRac&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs" target="_blank">DevExPress Course</a>

- DevExpress v19.2

### 2. <a href="https://www.youtube.com/watch?v=TZjkvn1WRac&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs" target="_blank">Login</a>

- 무료 아이콘 사이트 : <a href="https://www.flaticon.com/" target="_blank">flaticon</a>
  - 이이콘은 32px로 저장
- formLogin 속성
  - FormBorderStyle : None
  - StartPosition : CenterScreen
- buttonLogin 속성
  - BackColor : MidnightBlue
  - Font : Century Gothic, 10pt, style = bold
  - ForeColor : white
  - FlatStyle : Flat
  - FlatAppearance : mouseOverBackColor = DarkBlue
- buttonExit 속성
  - BackColor : Transparent
  - FlatStyle : Flat
  - FlatAppearance : BorderSize = 0, mouseOverBackColor = DarkBlue

```csharp
public partial class FormLogin : Form
{
    public FormLogin()
    {
        InitializeComponent();
    }

    private void pictureBox2_Click(object sender, EventArgs e)
    {
        System.Windows.Forms.Application.Exit(); // exit
    }

    private void pictureBox3_Click(object sender, EventArgs e)
    {
        this.WindowState = FormWindowState.Minimized; // minimize
    }

    private void buttonLogin_Click(object sender, EventArgs e)
    {
        string userId = textBoxUserId.Text.Trim();
        string password = textBoxPassword.Text.Trim();

        if (userId == "" || password == "")
        {
            MessageBox.Show("Bad Userid or Password", "Exclamation", MessageBoxButtons.OK, MessageBoxIcon.Exclamation);
            return;
        }
            
        FormMain formMain = new FormMain();
        formMain.Show();  // FormMain 일반 창으로 표시
        this.Hide();  // FormLogin 숨기기

        // FormMain이 닫힐 때 프로그램 종료
        formMain.FormClosed += (s, args) => Application.Exit();
    }
}
```

### 3. <a href="https://www.youtube.com/watch?v=GFPw0jIGdlQ&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=9" target="_blank">RibbonForm</a>

- DeaultLookAndFeel : SkinName = Office 2016 Colorful

### 4. <a href="https://www.youtube.com/watch?v=XuqjMa94VRE&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=10" target="_blank">TileControl</a>

- LayoutControl : Dock(Fill), TextPosition, HideText
- TileControl : Add Group -> Add Large Item -> Add Small Item -> Add Wide Item
  - Select TileItem Template
  - Edit TileItem Elements : Text, Image, Appearance -> Normal -> Font(폰트바꾸기)

### 5. <a href="https://www.youtube.com/watch?v=Rdf9smqlPVI&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=11" target="_blank">ToolbarForm</a>

### 6. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=12" target="_blank">FluentDesignForm</a>

### 7. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=13" target="_blank">UI-ReadyForm</a>

- dataLayoutControl1을 지우고, devForm의 layoutControl1을 카피해서 붙여넣어야 함

### 8. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=14" target="_blank">UI-ReadyForm2</a>

### 9. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=15" target="_blank">DXValidationProvider</a>

- DXValidationProvider : 유효성 체크
  - 속성 : ValidationMode(auto)
  - Customize Validation Rules -> Control(textEditID) -> Conditional Validation -> ConditionOperater(IsNotBlank) -> ErrorType(Critical)

### 10. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=16" target="_blank">PdfViewer</a>

- form 속성 : startPosition = CenterScreen
- pdfViewer > Dock in parent container > Create Ribbon > Create "PDF Viewer" Bar > Create "Comment" Bar

### 11. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=20" target="_blank">LoginForm</a>

- textPasword 속성 : UseSystemPasswordChar = true
- label 속성 : TextAlignment = MiddleCenter

### 12. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=21" target="_blank">Table 만들기</a>

- id : primary key 설정, 속성(ID사양 -> true)
- createTime : 속성(기본값 또는 바인딩 -> getdate())
- 테이블 스키마 설정후 crtl + s -> 테이블 이름 입력

### 13. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=22" target="_blank">SQL Server Object Explorer</a>

- SQL Server Object Explorer -> Add New Connection
  - Local -> 서버이름(컴퓨터이름) -> 인증(SQL Server 인증) -> 사용자이름, 비밀번호, 데이터베이스이름
  - 속성 : 연결 문자열 복사
- LoginForm
  - 속성 : StartPosition = CenterScreen

```csharp
public partial class LoginForm : DevExpress.XtraEditors.XtraForm
{
    private void LoginButton_Click(object sender, EventArgs e)
    {
        DashboardForm dashboard = new DashboardForm();
        dashboard.Show();
    }
}
```

### 14. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=23" target="_blank">DashboardForm</a>

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

### 15. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=24" target="_blank">GUI</a>

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

### 16. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=26" target="_blank">Project Architecture</a>

- Project Architecture
  - Data Access Layer : DB 연결 및 CRUD 처리 (DAL)
  - Business Logic Layer : 비즈니스 로직 처리 (BLL)
  - Presentation Layer : UI 처리 (GUI)

- BLL : Csharp Class 추가
  - StudentBLL.cs

### 17. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=27" target="_blank">DAL</a>

- DAL : Csharp Class 추가
  - StudentDAL.cs
