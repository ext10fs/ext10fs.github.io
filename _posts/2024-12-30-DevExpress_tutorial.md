---
layout: single
title: "DevExpress-tutorial"
categories: WinForm
tags: [Csharp, WinForm, devexpress]
---

### <a href="https://www.youtube.com/watch?v=iQ-vh1TMSCA&list=PL-EU0JUF-XD1rMTFIuhiLjEwyc0yqNbWt&index=3" target="_blank">1.Layout Control</a>

- 폼의 layout 정보를 파일에 저장하고 로드하는 예제

```csharp
private void XtraForm1_Load(object sender, EventArgs e)
{
    string fileName = string.Format("{0}/{1}.xml", Application.StartupPath, this.Name);
    if (File.Exists(fileName))
    {
        //form의 Layout 정보를 XtraForm1.xml 파일에서 읽어온다.
        layoutControl1.RestoreLayoutFromXml(fileName);
    }

}

private void simpleButton1_Click(object sender, EventArgs e)
{
    try
    {
        //현재 layout 정보를 XtraForm1.xml 파일에 저장한다.
        layoutControl1.SaveLayoutToXml(string.Format("{0}/{1}.xml", Application.StartupPath, this.Name));
    }
    catch (Exception ex)
    {
        XtraMessageBox.Show(ex.Message, "Message", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}
```

### <a href="https://www.youtube.com/watch?v=FhYCUhaDEt4" target="_blank">2.Read Excel</a>

- Excel 파일을 읽어와서 DataSet에 저장하는 예제 : <a href="https://github.com/ext3fs/DevExpress_Tutorial_Solution/tree/main/ReadExcel" target="_blank">GitHub</a>
  - layoutControl
  - dataGridView
  - TextEdit : readOnly (true)
  - comboBox : window 공용 컨트롤
  - Button : window 공용 컨트롤을 도구상자에서 끌어와서 사용, 그렇지 않으면 layout이 깨짐

- 프로젝트 오른쪽 클릭 > NuGet 패키지 관리 > 찾아보기
  - ExcelDataReader, ExcelDataReader.DataSet
  - Z.Dapper.Plus 라이브러리 설치

- SQL Sever Management Studio에서 Northwind DB를 Test DB로 복사
  - Northwind DB > Customers table > 테이블 스크립팅 > Create > 새쿼리편집기창 > table 생성 스크립트 복사
  - Test DB 선택 > 새쿼리 > table 생성 스크립트 붙여넣기 > 실행
  - Northwind DB > Customers table select 한후 .CSV로 저장 > 웹에서 .CSV to .XLSX 변환

- 데이터 소스 만들기
  - 새 데이터 소스 만들기 > 데이터베이스 > DataSet
  - 데이터 연결 선택 : SQL Server 자동 감지됨, 중요한 데이터 포함 체크 -> App.config에 연결 문자열 생성됨
  - 데이터베이스 개체 선택 : Customers 테이블 선택, 데이터 세트 이름 -> testDataSet

```csharp
// App.config에 생성된 연결 문자열
<connectionStrings>
    <add name="WindowsFormsApp1.Properties.Settings.testConnectionString"
        connectionString="Data Source=localhost;Initial Catalog=test;Persist Security Info=True;User ID=ilyoung;Password=1234"
        providerName="System.Data.SqlClient" />
</connectionStrings>
```

- dataGridView 오른쪽 상단 화살표 클릭 > 데이터 소스 선택 > 프로젝트 데이터 소스 추가 > customers 테이블 선택
  - 데이터 소스 선택이 잘못됐을 경우 form 아래의 컨트롤들을 삭제하면 클리어됨
  - Form을 삭제하고 다시 만드는 방법도 있음
- Customers.cs 클래스 생성

```csharp
class Customer
{
    public string CustomerID { get; set; }
    public string CompanyName { get; set; }
    public string ContactName { get; set; }
    public string ContactTitle { get; set; }
    public string Address { get; set; }
    public string City { get; set; }
    public string Region { get; set; }
    public string PostalCode { get; set; }
    public string Country { get; set; }
    public string Phone { get; set; }
    public string Fax { get; set; }
}
```

```csharp
public partial class Form1 : Form
{
    DataTableCollection tableCollection;

    public Form1()
    {
        InitializeComponent();
    }

    private void Form1_Load(object sender, EventArgs e)
    {
         // TODO: 이 코드는 데이터를 'testDataSet.Customers' 테이블에 로드합니다. 필요 시 이 코드를 이동하거나 제거할 수 있습니다.
        this.customersTableAdapter.Fill(this.testDataSet.Customers);
    }

    private void BtnFileOpen_Click(object sender, EventArgs e)
    {
        using (OpenFileDialog ofd = new OpenFileDialog() { Filter = "Excel 97-2003 Workbook|*.xls|Excel Workbook|*.xlsx" })
        {
            if (ofd.ShowDialog() == DialogResult.OK)
            {
                TxtFileName.Text = ofd.FileName;
                using (var stream = File.Open(ofd.FileName, FileMode.Open, FileAccess.Read))
                {
                    using (IExcelDataReader reader = ExcelReaderFactory.CreateReader(stream) )
                    {
                        DataSet ds = reader.AsDataSet(new ExcelDataSetConfiguration()
                        {
                            ConfigureDataTable = (_) => new ExcelDataTableConfiguration()
                            {
                                UseHeaderRow = true
                            }
                        });

                        tableCollection = ds.Tables;
                        CbxSheet.Items.Clear();
                        foreach (DataTable table in tableCollection)
                            CbxSheet.Items.Add(table.TableName);
                    }
                }
            }
        }
    }

    private void CbxSheet_SelectedIndexChanged(object sender, EventArgs e)
    {
        DataTable dt = tableCollection[CbxSheet.SelectedItem.ToString()];
        //dataGridView1.DataSource = dt;
        if (dt == null)
            return;

        List<Customer> customers = new List<Customer>();
        for (int i = 0; i < dt.Rows.Count; i++)
        {
            Customer customer = new Customer();
            customer.CustomerID = dt.Rows[i]["CustomerID"].ToString();
            customer.CompanyName = dt.Rows[i]["CompanyName"].ToString();
            customer.ContactName = dt.Rows[i]["ContactName"].ToString();
            customer.ContactTitle = dt.Rows[i]["ContactTitle"].ToString();
            customer.Address = dt.Rows[i]["Address"].ToString();
            customer.City = dt.Rows[i]["City"].ToString();
            customer.Region = dt.Rows[i]["Region"].ToString();
            customer.PostalCode = dt.Rows[i]["PostalCode"].ToString();
            customer.Country = dt.Rows[i]["Country"].ToString();
            customer.Phone = dt.Rows[i]["Phone"].ToString();
            customer.Fax = dt.Rows[i]["Fax"].ToString();
            customers.Add(customer);
        }
        customersBindingSource.DataSource = customers;
    }

    private void BtnImport_Click(object sender, EventArgs e)
    {
        try
        {
            string connStr = "Server=localhost;Database=test;User Id = ilyoung; Password=1234;";
            DapperPlusManager.Entity<Customer>().Table("Customers");
            List<Customer> customers = customersBindingSource.DataSource as List<Customer>;
            if (customers != null)
            {
                using (IDbConnection db = new SqlConnection(connStr))
                {
                    db.BulkInsert(customers);
                }
            }
            MessageBox.Show("insert success!!");
        }
        catch (Exception ex)
        {
            MessageBox.Show(ex.Message, "Message", MessageBoxButtons.OK, MessageBoxIcon.Error);
        }
    }
}
```

### <a href="https://www.youtube.com/watch?v=6fqD_spnDhg" target="_blank">3.Invoice</a>

- Invoice 문서를 만드는 예제 : <a href="https://github.com/ext3fs/DevExpress_Tutorial_Solution/tree/main/Invoice" target="_blank">GitHub</a>
  - layoutControl
  - GridControl : GridControl 먼저 깔아야 레이아웃 깨지지 않음
  - DateEdit
  - SimpleButton : size Constraints > Lock Size (윈도우 사이즈 변해도 같은 크기 유지)

- orders.cs 클래스 생성

```csharp
public class Orders
{
    public int OrderId { get; set; }
    public string CustomerId { get; set; }
    [Display(Name = "Contact Name")]
    public string ContactName { get; set; }
    public string Address { get; set; }
    [Display(Name = "Postal Code")]
    public string PostalCode { get; set; }
    public string City { get; set; }
    public string Phone { get; set; }
    public DateTime OrderDate { get; set; }
}
```

- orderDetails.cs 클래스 생성

```csharp
public class OrderDetails
{
    public int OrderId { get; set; }
    [Display(Name = "Product Name")]
    public string ProductName { get; set; }
    public int Quantity { get; set; }
    public Decimal DisCount { get; set; }
    [Display(Name = "Unit Price")]
    public Decimal UnitPrice { get; set; }
    public Decimal Total
    {
        get
        {
            return Quantity * UnitPrice - Quantity * UnitPrice * DisCount;
        }
    }
}
```

- NuGet 패키지 관리
  - Dapper 라이브러리 설치

- GridControl 속성에서
  - Chose DataSource > 프로젝트 데이터 소스 추가 > 개체 > orders 선택
  - run Designer > Columns > colCustomerId > Visible (false) : gridControl에서 해당 컬럼이 안보이게 함

```csharp
// form1.cs
private void Form1_Load(object sender, EventArgs e)
{
    // 최근 1달 기간 설정
    DateTime today = DateTime.Today;
    DateTime oneMonthAgo = today.AddMonths(-1);
    DtFromDate.DateTime = oneMonthAgo;
    DtToDate.DateTime = today;
}

private void BtnLoadData_Click(object sender, EventArgs e)
{
    try
    {
        using (IDbConnection db = new SqlConnection(ConfigurationManager.ConnectionStrings["cn"].ConnectionString))
        {
            if (db.State == ConnectionState.Closed)
                db.Open();

            // DateEdit의 값을 DateTime으로 변환
            DateTime fromDate = Convert.ToDateTime(DtFromDate.EditValue);
            DateTime toDate = Convert.ToDateTime(DtToDate.EditValue);

            string query = @"
                SELECT o.OrderID, c.CustomerID, c.ContactName, c.Address, 
                       c.PostalCode, c.City, c.Phone, o.OrderDate
                FROM Orders o 
                INNER JOIN Customers c ON o.CustomerID = c.CustomerID
                WHERE o.OrderDate BETWEEN @FromDate AND @ToDate";

            // Dapper의 매개변수 사용
            var parameters = new { FromDate = fromDate, ToDate = toDate };
            ordersBindingSource.DataSource = db.Query<Orders>(query, parameters);
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show($"오류 발생: {ex.Message}", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}

private void BtnPrint_Click(object sender, EventArgs e)
{
    Orders obj = ordersBindingSource.Current as Orders;
    if (obj == null)
        return;

    try
    {
        using (IDbConnection db = new SqlConnection(ConfigurationManager.ConnectionStrings["cn"].ConnectionString))
        {
            if (db.State == ConnectionState.Closed)
                db.Open();

            // 공백때문에 [Order Details]
            string query = @"
                SELECT d.OrderID, p.ProductName, d.Quantity, d.Discount, d.UnitPrice
                FROM [Order Details] d 
                INNER JOIN Products p ON d.ProductID = p.ProductID
                WHERE d.OrderID = @OrderID";

            // Dapper의 매개변수 사용
            var parameters = new { OrderID = obj.OrderId };
            var list = db.Query<OrderDetail>(query, parameters).ToList();
            using (var frm = new XfrmPrint())
            {
                frm.PrintInvoice(obj, list);
                frm.ShowDialog();
            }
        }
    }
    catch (Exception ex)
    {
        MessageBox.Show($"오류 발생: {ex.Message}", "Error", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}
```

- 프로젝트 > 추가 > 새 항목 > report > DevExpress v21.1 Report > empty report
  - 하단의 Preview 버튼 클릭 > Page Setup > Size(A4), Page Margins (0.7, 0.7, 0.7, 0.7)
  - Insert Band > ReportHeader : 도구상자 > XRLabel 속성 > TextAlignment (MiddleCenter, MiddleLeft)
  - Insert Band > ReportFooter : 도구상자 > XRPageInfo 톱니바퀴 > TextFormatString (Page {0}/{1})
  - Detail > 도구상자 > XRTable 속성 > Padding, Font, Border
    - No 필드 > Summary Editer : Summary Running (Report), Summary Function (RecordNumber)
  - 왼쪽 상단 화살표 클릭 > Data Source > Add Report Data Source > Object > Invoice > OrderDetails > retrieve the data source schema
  - Detail 테이블의 각 칼럼에 > Table Cell Task > Expression > 필드 선택
  - ReportFooter > 도구상자 > XRTable > 칼럼 속성 : Border, TextAlignment, padding
    - Total 필드 > Summary Editer : Summary Running(Report), Summary Function(Sum), Argument Expression(Total)
  - 최상단 XtraReports > Field list > Parameters 오른쪽 클릭
    - Add Parameter > Name(pCustomerName), Type(String)
    - Add Parameter > Name(pOrderId), Type(String)
    - Add Parameter > Name(pAddress), Type(String)
    - Add Parameter > Name(pPhone), Type(String)
    - Add Parameter > Name(pPostalCode), Type(String)
    - Add Parameter > Name(pCity), Type(String)
    - Add Parameter > Name(pDate), Type(Date)
    - Field List 에서 끌어서 해당 위치에 놓기

- 프로젝트 > Add DevExpress Item > from > XfrmPrint.cs
  - 도구상자 > DocumentViewer > Create Standard Toolbar > StartPosition(CenterParent), WindowState(Maximized)

- Preview를 보면서, 각 필드의 Fromat String을 적절히 설정 : Number, DateTime, Currency

```csharp
// XfrmPrint.cs
public void PrintInvoice(Orders order, List<OrderDetail> data)
{
    var report = new XtraReport1();
    foreach (var p in report.Parameters)
        p.Visible = false; // 파라미터 숨김 -> 주석처리하고 실행시켜보면 이유를 알 수 있음

    report.InitData(order.OrderId.ToString(), order.OrderDate, order.CustomerId, order.Address, order.PostalCode, order.City, order.Phone, data);
    documentViewer1.DocumentSource = report;
    report.CreateDocument();
}

// XtraReport1.cs
public void InitData(string orderid, DateTime orderdate, string customer, string adderss, string postalcode, string city, string phone, List<OrderDetail> data)
{
    pOrderID.Value = orderid;
    pDate.Value = orderdate.Date;
    pCustomerName.Value = customer;
    pAddress.Value = adderss;
    pPostalCode.Value = postalcode;
    pCity.Value = city;
    pPhone.Value = phone;
    objectDataSource1.DataSource = data;
}
```

### <a href="https://www.youtube.com/watch?v=oaXK-XhwF8g&t=4s" target="_blank">4.Invoice2</a>

- Invoice 문서를 만드는 예제 2 : <a href="https://github.com/ext3fs/DevExpress_Tutorial_Solution/tree/main/Invoice2" target="_blank">GitHub</a>

### <a href="https://www.youtube.com/watch?v=BtOEztT1Qzk&t=1290s" target="_blank">5.Modern UI</a>

- Modern UI : <a href="https://github.com/ext3fs/DevExpress_Tutorial_Solution/tree/main/Modern_UI" target="_blank">GitHub</a>
  - Panel : Dock(Left), BackColor(51,51,76), Size(220, 584)
  - Panel : Name(panelLogo), Dock(top), BackColor(39,39,58), Size(220, 80)
  - Button : Name(btnProducts) Dock(Top), ForeColor(Gainsboro), FlatStyle(flat), FlatAppearance(border:0), Size(220, 60), Image(grocery-store_32px.png), ImageAlign(MiddleLeft), TextAlign(MiddleLeft), TextImageRelation(ImageBeforeText), Text(  Products), Padding(12,0,0,0), Font(Microsoft Sans Serif, 9pt)
  - Image는 flaticon에서 검은색 배경의 아이콘 > Edit Icon > Choose a New Color > #FFFFFF > Download 32px

- add class > ThemeColor.cs

```csharp
public static class ThemeColor
{
    public static Color PrimaryColor { get; set; }
    public static Color SecondaryColor { get; set; }
    public static List<string> ColorList = new List<string>()
    {
        "#3F51B5", "#009688", "#FF5722", "#607D8B", "#FF9800", "#9C27B0",
        "#2196F3", "#EA676C", "#E41A4A", "#5978BB", "#018790", "#0E3441",
        "#00B0AD", "#721D47", "#EA4833", "#EF937E", "#F37521", "#A12059",
        "#126881", "#8BC240", "#364D5B", "#C7DC5B", "#0094BC", "#E4126B",
        "#43B76E", "#7BCFE9", "#B71C46"
    };

    public static Color ChangeColorBrightness(Color color, double correctionFactor)
    {
        double red = color.R;
        double green = color.G;
        double blue = color.B;
        
        if (correctionFactor < 0) //If correction factor is less than 0, darken color.
        {
            correctionFactor = 1 + correctionFactor;
            red *= correctionFactor;
            green *= correctionFactor;
            blue *= correctionFactor;
        }
        else //If correction factor is greater than zero, lighten color.
        {
            red = (255 - red) * correctionFactor + red;
            green = (255 - green) * correctionFactor + green;
            blue = (255 - blue) * correctionFactor + blue;
        }

        return Color.FromArgb(color.A, (byte)red, (byte)green, (byte)blue);
    }
}
```



4:00
