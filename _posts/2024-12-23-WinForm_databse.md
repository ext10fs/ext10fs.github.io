---
layout: single
title: "Windows Forms - database"
categories: WinForm
tags: [Csharp, WinForm, MsSQL]
---

### 1. ADO.net (ActiveX data objects)

- .NET 프레임워크에서 데이터 액세스를 위한 데이터 접근 기술
- 다양한 데이터 소스(예: SQL Server, Oracle, MySQL, XML 파일 등)와 통신 가능
- 분류
  - 연결형 : 실시간으로 연결된 형태
  - 비연결형(메모리형 DB) : 데이터를 읽거나 쓸때만 연결된 형태
- 구성 요소
  - DataProvider : 데이터 소스와 통신하는 객체 (Connection, Command, DataReader, DataAdapter)
  - DataSet : 데이터 소스의 데이터를 저장하는 객체 (DataTableCollection, DataRelationCollection)
- 기본 클래스
  - System.Data : namespace
  - System.Data.SqlClient : DataProvider
  - System.Data.SqlTypes : Sql Server 데이터형 클래스

### 2. SqlConnection

- 데이터베이스에 연결하는 정보를 담은 객체 (정보만 셋팅, 실제 연결하지는 않음)
- integrated security
  - 사용자 ID와 암호 : integrated security = false
  - 윈도우즈 인증 : integrated security = true
  - 생략시 기본값은 false

```csharp
SqlConnection conn = null;

private void Form1_Load(object sender, EventArgs e)
{
    textBox1.Text = "no setting";
}

private void button1_Click(object sender, EventArgs e)
{
    // connection string은 대소문자 구분 안 함
    string str = "Server=" + "localhost" + ";"
        + "Database=" + textBox2.Text + ";"
        + "User Id=" + textBox3.Text + ";"
        + "Password=" + textBox4.Text + ";";

    conn?.Dispose();
    conn = new SqlConnection(str); // 정보만 셋팅
    textBox1.Text = (conn == null) ? "no setting" : "setting";
}

private void button2_Click(object sender, EventArgs e)
{
    conn.Open(); // 실제로 연결

    if (conn.State == ConnectionState.Open)
        MessageBox.Show("DB Open succ");
    else
        MessageBox.Show("DB Open fail");
}

private void button3_Click(object sender, EventArgs e)
{
    conn.Close(); // Open이 안되어 있더라도 에러 안 남, Dispose 호출 시 자동 호출

    if (conn.State == ConnectionState.Closed)
        MessageBox.Show("DB Close succ");
    else
        MessageBox.Show("DB Close fail");
}

private void button4_Click(object sender, EventArgs e)
{
    conn.Dispose();
    conn = null;
    textBox1.Text = "해제";
    MessageBox.Show("Connection Dispose");
}
```

### 3. 연결형 데이터 접근

- SqlCommand
  - SQL 명령을 실행
  - 주요 속성
    - CommandText : SQL 명령
    - Connection : 연결 객체
    - CommandType : CommandType.Text(SQL), CommandType.TableDirect(테이블 명령), CommandType.StoredProcedure
  - 주요 메서드
    - ExecuteNonQuery() : 영향받은 레코드 수 반환
    - ExecuteReader() : SQL을 Connecton에 보내고 결과를 읽기 위한 SqlDataReader 객체 반환
- SqlDataReader
  - SqlDataReader가 반환되었다면 이미 데이터는 로드 되었음, 하지만 효율적 메모리 사용을 위해 일부만 로드 되어 있음
  - 데이터 일부만 로드 되었다면 reader.Read()로 한 레코드씩 읽는 중에 나머지 데이터를 로드 함
  - 주요 속성 : Connection, FieldCount, HasRows, RecordsAffected
  - 주요 메서드 : Close(), Read()

- <a href="https://youtu.be/8PJXHmaHZds" target="_blank">예제</a>

```csharp
//textBox에 nonQuery(리턴값이 없는 쿼리) 입력
private void button1_Click(object sender, EventArgs e)
{
    using (var conn = new SqlConnection())
    {
        try
        {
            listView1.Clear();
            conn.ConnectionString = "Server=localhost;Database=test;User Id=ilyoung;Password=1234;";
            conn.Open();

            using (var cmd = new SqlCommand(textBox1.Text, conn))
            {
                if(textBox1.Text.Length != 0)
                    cmd.ExecuteNonQuery();
                
                cmd.CommandText = "select * from tbl_student";
                using (var reader = cmd.ExecuteReader())
                {
                    // listView의 view 속성을 List로 변경해야 스트링 형식으로 출력 가능
                    while (reader.Read())
                        listView1.Items.Add(string.Format($"{reader["name"]} {reader["age"]} {reader["male"]}"));
                }
            }
        }
        catch (Exception ex)
        {
            listView1.Items.Add("DB 에러: " + ex.Message);
        }
    }
}
```

### 4. 비연결형 데이터 접근 (메모리형 DataBase)

- DataSet : DataTable들의 집합
  - DataTable : DB 테이블 구조와 데이터를 저장하는 객체
  - DataRow : 테이블의 행을 나타내는 객체
  - DataColumn : 테이블의 열을 나타내는 객체

### 5. DataTable

- DB의 테이블과 동일한 구조를 가지고 있는 객체
- DB 쿼리 후의 결과를 DataTable 객체에 저장하여 사용

```csharp
static void Main()
{
    var tbl = new DataTable();

    tbl.Columns.AddRange(new DataColumn[]
    {
        new DataColumn("name", typeof(string)),
        new DataColumn("age", typeof(int)),
        new DataColumn("male", typeof(bool))
    });

    // primary key 설정
    tbl.PrimaryKey = new DataColumn[] { tbl.Columns["name"] };

    // 데이터 입력
    tbl.Rows.Add("철수", 9, true);
    tbl.Rows.Add("수지", 28, false);
    tbl.Rows.Add("미자", 29, false);
    
    // 객체 순회
    foreach (DataRow row in tbl.Rows)
        Console.WriteLine($"이름:{row["name"]} 나이:{row["age"]} 성별:{row["male"]}");

    // 조건 검색
    DataRow[] rows = tbl.Select("age >= 100"); // 조건은 문자열로 작성
    foreach (var row in rows)
        Console.WriteLine($"이름:{row["name"]} 나이:{row["age"]} 성별:{row["male"]}");
}
```

- <a href="https://youtu.be/kQR_Ql3p-zc" target="_blank">DataGridView 예제</a>

```csharp
DataTable tbl;

private void Form1_Load(object sender, EventArgs e)
{
    tbl = new DataTable();

    tbl.Columns.AddRange(new DataColumn[]
    {
        new DataColumn("name", typeof(string)),
        new DataColumn("age", typeof(int)),
        new DataColumn("male", typeof(bool))
    });

    tbl.Rows.Add("철수", 9, true);
    tbl.Rows.Add("수지", 28, false);
    tbl.Rows.Add("미자", 29, false);
    
    //셀을 선택하면 해당 row가 전부 선택됨
    dataGridView1.SelectionMode = DataGridViewSelectionMode.FullRowSelect;
    dataGridView1.MultiSelect = false; //한 행만 선택
    dataGridView1.DataSource = tbl;
}

private void insertBtn_Click(object sender, EventArgs e)
{
    tbl.Rows.Add(textBox1.Text, int.Parse(textBox2.Text), radioButton1.Checked);
}

private void deleteBtn_Click(object sender, EventArgs e)
{
    //1. DataTable의 서브 객체인 DataRow를 이용
    var idx = dataGridView1.SelectedRows[0].Index; // MultiSelect가 false이므로 
    DataRow row = tbl.Rows[idx];
    row.Delete(); // row 속성에만 삭제 표시
    tbl.AcceptChanges(); // 실제로 삭제
    
    // 2. DataGridView의 서브 객체인 DataGridViewRow를 이용
    //foreach (DataGridViewRow row in dataGridView1.SelectedRows) // multi row인 경우
    //    dataGridView1.Rows.Remove(row);
}

private void updateBtn_Click(object sender, EventArgs e)
{
    var idx = dataGridView1.SelectedRows[0].Index; 
    DataRow row = tbl.Rows[idx];
    row.BeginEdit();
    row["name"] = textBox1.Text;
    row["age"] = int.Parse(textBox2.Text);
    row["male"] = radioButton1.Checked;
    row.EndEdit();
}
```

### 6. DataSet

- DataSet.Tables : DataTable 컬렉션
- 테이블명 대소문자 구별
- XML 파일로 저장 및 로드 가능
- DataRelation : 테이블 간의 관계 설정 가능 (여기서는 다루지 않음)

- <a href="https://youtu.be/Ars6Tu_9orA" target="_blank">DataSet 예제</a>

```csharp
//버튼을 클릭하면 student 테이블과 major 테이블을 연결하여 출력
DataSet dataSet;

private void Form1_Load(object sender, EventArgs e)
{
    dataSet = new DataSet();
    
    // major 테이블 생성 및 열 추가
    DataTable majorTable = dataSet.Tables.Add("major");
    majorTable.Columns.Add(new DataColumn("id", typeof(int))
    {
        AutoIncrement = true,
        AutoIncrementSeed = 1, // 시작 값
        AutoIncrementStep = 1 // 증가 값
    });
    majorTable.PrimaryKey = new DataColumn[] { majorTable.Columns["id"] };
    majorTable.Columns.Add(new DataColumn("name", typeof(string)) { MaxLength = 50, AllowDBNull = false });
    
    // 데이터 행 추가
    majorTable.Rows.Add(new object[] { null, "통계학과" });
    majorTable.Rows.Add(new object[] { null, "불문학과" });
    
    // student 테이블 생성 및 열 추가
    DataTable studentTable = dataSet.Tables.Add("student");
    studentTable.Columns.Add(new DataColumn("id", typeof(int))
    {
        AutoIncrement = true,
        AutoIncrementSeed = 1,
        AutoIncrementStep = 1
    });
    studentTable.PrimaryKey = new DataColumn[] { studentTable.Columns["id"] };
    studentTable.Columns.Add(new DataColumn("name", typeof(string)) { MaxLength = 50, AllowDBNull = false });
    studentTable.Columns.Add(new DataColumn("major_id", typeof(int)) { AllowDBNull = false });
    
    // 외래키 제약조건 추가
    var fk = new ForeignKeyConstraint("FK_student_major", majorTable.Columns["id"], studentTable.Columns["major_id"])
    {
        UpdateRule = Rule.Cascade, // 부모 키를 업데이트하면 자동으로 자식 테이블의 참조키가 변경됨 
        DeleteRule = Rule.Cascade  // 부모 키를 삭제하면 자동으로 자식 테이블의 row가 삭제됨
    };
    studentTable.Constraints.Add(fk);
    
    // 데이터 행 추가
    studentTable.Rows.Add(new object[] { null, "윤영주", 2 });
    studentTable.Rows.Add(new object[] { null, "정일영", 1 });
    studentTable.Rows.Add(new object[] { null, "김관식", 1 });

    dataGridView1.SelectionMode = DataGridViewSelectionMode.FullRowSelect;
    dataGridView1.MultiSelect = false;
    dataGridView1.DataSource = studentTable;

    dataGridView2.SelectionMode = DataGridViewSelectionMode.FullRowSelect;
    dataGridView2.MultiSelect = false;
    dataGridView2.DataSource = majorTable;
}

private void button1_Click(object sender, EventArgs e)
{
    if (dataGridView1.SelectedRows.Count == 0)
        return;

    var studentTable = dataSet.Tables["student"];
    var majorTable = dataSet.Tables["major"];
    
    var studentRow = studentTable.Rows[dataGridView1.SelectedRows[0].Index];
    var majorRow = majorTable.Select($"id = {studentRow["major_id"]}").FirstOrDefault();
    
    string str;
    if (majorRow == null)
        str = $"학생: {studentRow["name"]}, 학과: {studentRow["major_id"]}";
    else
        str = $"학생: {studentRow["name"]}, 학과: {majorRow["name"]}";
    MessageBox.Show(str);
}
```

### 7. DataAdapter

- 역할
  - DB 연결 : open(), close() 불필요
  - DataSet 채우기 : fill()
  - DataSet의 내용을 SQL Server에 업데이트

- <a href="https://youtu.be/vhTPynuMJAQ" target="_blank">DataAdapter 예제</a>

```csharp
// ExecuteNonQuery()로 구현(예전 방식)
private DataSet dataSet;
private SqlConnection conn;
private const string CONNECTION_STRING = "Server=localhost;Database=test;User Id=ilyoung;Password=1234;";
private SqlDataAdapter dataAdapter;

private void SetDataGridView()
{
    dataSet.Clear();

    string query = "SELECT * FROM Table_student";
    using (var cmd = new SqlCommand(query, conn))
    {
        dataAdapter.SelectCommand = cmd;
        dataAdapter.Fill(dataSet, "Table_student");
        dataGridView1.DataSource = dataSet.Tables["Table_student"];
    }
}

private void Form1_Load(object sender, EventArgs e)
{
    try
    {
        dataAdapter = new SqlDataAdapter();
        dataSet = new DataSet();

        conn = new SqlConnection(CONNECTION_STRING); //SqlConnection 객체에 셋팅만 해줌
        conn.Open(); // 실제 DB connect

        SetDataGridView();
    }
    catch (Exception ex)
    {
        MessageBox.Show($"Form1_Load() : {ex.Message}", "Exception", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}

private void InsertButton_Click(object sender, EventArgs e)
{
    try
    {
        string query = "INSERT INTO Table_student (name, majorNo) VALUES(@name, @majorNo)";
        using (var cmd = new SqlCommand(query, conn))
        {
            cmd.Parameters.AddWithValue("@name", textBox1.Text.Trim());
            cmd.Parameters.AddWithValue("@majorNo", textBox2.Text.Trim());
            dataAdapter.InsertCommand = cmd;
            dataAdapter.InsertCommand.ExecuteNonQuery();
        }

        SetDataGridView();
    }
    catch (Exception ex)
    {
        MessageBox.Show($"InsertButton_Click() : {ex.Message}", "Exception", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}

private void button3_Click(object sender, EventArgs e)
{
    if (dataGridView1.CurrentRow == null)
        return;

    try
    {
        string query = "UPDATE Table_student SET name = @name, majorNo = @majorNo WHERE no = @no";
        using (var cmd = new SqlCommand(query, conn))
        {
            cmd.Parameters.AddWithValue("@name", textBox1.Text.Trim());
            cmd.Parameters.AddWithValue("@majorNo", textBox2.Text.Trim());
            cmd.Parameters.AddWithValue("@no", dataGridView1.CurrentRow.Cells["no"].Value);
            dataAdapter.UpdateCommand = cmd;
            dataAdapter.UpdateCommand.ExecuteNonQuery();
        }
        
        SetDataGridView();
    }
    catch (Exception ex)
    {
        MessageBox.Show($"button2_Click() : {ex.Message}", "Exception", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}

private void DeleteButton_Click(object sender, EventArgs e)
{
    try
    {
        if (dataGridView1.CurrentRow == null)
            return;

        string query = "DELETE FROM Table_student WHERE no = @no";
        using (var cmd = new SqlCommand(query, conn))
        {
            cmd.Parameters.AddWithValue("@no", dataGridView1.CurrentRow.Cells["no"].Value);
            dataAdapter.DeleteCommand = cmd;
            dataAdapter.DeleteCommand.ExecuteNonQuery();
        }

        SetDataGridView();
    }
    catch (Exception ex)
    {
        MessageBox.Show($"DeleteButton_Click() : {ex.Message}", "Exception", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}

private void Form1_FormClosing(object sender, FormClosingEventArgs e)
{
    conn?.Close();
    conn?.Dispose();
}
```

- SqlCommandBuilder : join이 없는 일반적인 SQL문을 자동 생성
  - 하나의 테이블만 처리 가능
  - 기본키가 반드시 있어야 한다
  - SqlDataAdapter 생성할때 select문을 반드시 명시해야 한다

```csharp
// SqlCommandBuilder로 구현
private DataSet dataSet;
private SqlConnection conn;
private const string CONNECTION_STRING = "Server=localhost;Database=test;User Id=ilyoung;Password=1234;";
private SqlDataAdapter dataAdapter;
private SqlCommandBuilder cmdBuilder;

private void RefreshDataGridView()
{
    dataSet.Tables["Table_student"].Clear();
    dataAdapter.Fill(dataSet, "Table_student");
    dataGridView1.DataSource = dataSet.Tables["Table_student"];
}

private void Form1_Load(object sender, EventArgs e)
{
    try
    {
        conn = new SqlConnection(CONNECTION_STRING);
        conn.Open();

        // SqlDataAdapter에 SELECT 구문을 할당하면, 이후 SqlCommandBuilder로 CRUD 명령어가 자동 생성됩니다.
        dataAdapter = new SqlDataAdapter("SELECT * FROM Table_student", conn);
        cmdBuilder = new SqlCommandBuilder(dataAdapter);

        dataSet = new DataSet();
        dataAdapter.Fill(dataSet, "Table_student");
        dataGridView1.DataSource = dataSet.Tables["Table_student"];
    }
    catch (Exception ex)
    {
        MessageBox.Show($"Form1_Load() : {ex.Message}", "Exception", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}

private void Form1_FormClosing(object sender, FormClosingEventArgs e)
{
    conn?.Dispose();
}

private void InsertButton_Click(object sender, EventArgs e)
{
    try
    {
        DataTable studentTable = dataSet.Tables["Table_student"];
        DataRow row = studentTable.NewRow();
        row["name"] = textBox1.Text.Trim();
        row["majorNo"] = textBox2.Text.Trim();
        studentTable.Rows.Add(row);
        dataAdapter.Update(dataSet, "Table_student"); // DataSet에 적용된 변경사항을 DB에 업데이트합니다.
        RefreshDataGridView();
    }
    catch (Exception ex)
    {
        MessageBox.Show($"InsertButton_Click() : {ex.Message}", "Exception", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}

private void updateButton_Click(object sender, EventArgs e)
{
    if (dataGridView1.CurrentRow == null)
        return;

    try
    {
        int idx = dataGridView1.CurrentRow.Index;
        DataTable studentTable = dataSet.Tables["Table_student"];
        DataRow row = studentTable.Rows[idx];
        row["name"] = textBox1.Text.Trim();
        row["majorNo"] = textBox2.Text.Trim();
        dataAdapter.Update(dataSet, "Table_student"); // 변경된 내용을 DB에 업데이트합니다.
        RefreshDataGridView();
    }
    catch (Exception ex)
    {
        MessageBox.Show($"updateButton_Click() : {ex.Message}", "Exception", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}

private void DeleteButton_Click(object sender, EventArgs e)
{
     if (dataGridView1.CurrentRow == null)
            return;

    try
    {
        int idx = dataGridView1.CurrentRow.Index;
        DataTable studentTable = dataSet.Tables["Table_student"];
        studentTable.Rows[idx].Delete();
        dataAdapter.Update(dataSet, "Table_student");  // 삭제된 내용을 DB에 업데이트합니다.
        RefreshDataGridView();
    }
    catch (Exception ex)
    {
        MessageBox.Show($"DeleteButton_Click() : {ex.Message}", "Exception", MessageBoxButtons.OK, MessageBoxIcon.Error);
    }
}
```
