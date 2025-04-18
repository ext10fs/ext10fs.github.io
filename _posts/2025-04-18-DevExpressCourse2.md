---
layout: single
title: "DevExpress Course 2"
categories: WinForm
tags: [Csharp, WinForm, DevExpress]
---

### 1. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=20" target="_blank">LoginForm</a>

- LoginForm 속성 : StartPosition = CenterScreen
- textPasword 속성 : UseSystemPasswordChar = true
- label 속성 : TextAlignment = MiddleCenter

### 2. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=21" target="_blank">Table 만들기</a>

- id : primary key 설정, 속성(ID사양 -> true)
- createTime : 속성(기본값 또는 바인딩 -> getdate())
- 테이블 스키마 설정후 crtl + s -> 테이블 이름 입력

### 3. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=22" target="_blank">SQL Server Object Explorer</a>

- SQL Server Object Explorer -> Add New Connection
  - Local -> 서버이름(컴퓨터이름) -> 인증(SQL Server 인증) -> 사용자이름, 비밀번호, 데이터베이스이름
  - 속성 : 연결 문자열 복사

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

### 4. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=23" target="_blank">DashboardForm</a>

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

### 5. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=24" target="_blank">GUI</a>

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

### 6. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=26" target="_blank">Project Architecture</a>

- Project Architecture
  - Data Access Layer : DB 연결 및 CRUD 처리 (DAL)
  - Business Logic Layer : 비즈니스 로직 처리 (BLL)
  - Presentation Layer : UI 처리 (GUI)

- BLL : Csharp Class 추가
  - StudentBLL.cs

### 7. <a href="https://www.youtube.com/watch?v=SF_vijg236U&list=PLMa5a9Dh6SlgqaMMd_E-GzRyJPZ0sOOhs&index=27" target="_blank">DAL</a>

- DAL : Csharp Class 추가
  - StudentDAL.cs
