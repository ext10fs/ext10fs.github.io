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
