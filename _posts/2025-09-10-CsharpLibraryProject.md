---
layout: single
title: "Csharp Library Project"
categories: Csharp
tags: [Csharp]
---

#### <a href="https://www.youtube.com/watch?v=SRk9NKv2uEo" target="_blank">1. Library Solution and Application Class</a>

- make empty solution
- add new project -> class library (.NET Framework)
- add folder (Tools)
- add class file (Tools/Application.cs)

```csharp
// Tools/Application.cs
public static class Application
{
    public static string Root
    {
        get
        {
            return AppDomain.CurrentDomain.BaseDirectory; // Application이 현재 실행중인 디렉토리 경로
        }
    }
}
```

- add new project -> Console App (.NET Framework) - 테스트용 프로젝트
- 시작 프로젝트로 설정
- Add Reference -> Projects -> check YoungLib

```csharp
class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine(YoungLib.Tools.Application.Root);
    }
}
```

#### <a href="https://www.youtube.com/watch?v=vbVaBg3wPj4" target="_blank">2. Log Manager Class</a>

- add class file (Tools/LogManager.cs)

```csharp
using System.IO;

public class LogManager
{
    private string _logPath;

    #region Constructors
    public LogManager(string path)
    {
        _logPath = path;

        //create directory
        if (!Directory.Exists(_logPath))
            Directory.CreateDirectory(_logPath); 
    }

    public LogManager() : this(Path.Combine(Application.Root, "Log"))
    {
    }
    #endregion
    
    #region Methods
    public void WriteLine(string msg)
    {
        string fileName = DateTime.Now.ToString("yyyy-MM-dd") + ".txt";
        string filePath = Path.Combine(_logPath, fileName);
        try
        {
            using (var writer = new StreamWriter(filePath, true))
            {
                string time = DateTime.Now.ToString("HH:mm:ss");
                writer.WriteLine(time + "\t" + msg);
            }
        }
        catch (Exception) // writing중에 오류가 나더라도 무시
        {
        }
    }
    #endregion
}
```

#### <a href="https://www.youtube.com/watch?v=zZ1bWfeVerU" target="_blank">3. Log Manager Class의 확장</a>

- logPath를 년도,월의 계층적 폴더로 구분
- 추후 Lazy 객체를 이용한 싱글톤 패턴을 적용하면 더 좋을 듯

```csharp
public class LogManager
{
    private readonly string _logPath;

    #region Constructors
    public LogManager(string path)
    {
        _logPath = path;
    }

    public LogManager() : this(Path.Combine(Application.Root, "Log"))
    {
    }
    #endregion
    
    #region Methods
    public void WriteLine(string msg)
    {
        string fileName = DateTime.Now.ToString("yyyy-MM-dd") + ".txt";
        string filePath = Path.Combine(_logPath, fileName.Substring(0,4), fileName.Substring(5, 2));
        
        //create directory
        if (!Directory.Exists(filePath))
            Directory.CreateDirectory(filePath);
        
        try
        {
            using (var writer = new StreamWriter(Path.Combine(filePath, fileName), true))
            {
                string time = DateTime.Now.ToString("HH:mm:ss");
                writer.WriteLine(time + "\t" + msg);
            }
        }
        catch (Exception) // writing중에 오류가 나더라도 무시
        {
        }
    }
    #endregion
}
```

#### <a href="https://www.youtube.com/watch?v=C5GO8JYYgCE" target="_blank">4. 확장 메서드</a>

- DLL을 사용하는 사용자라면 코드에 메서드를 추가할 수 없으므로 -> 확장 메서드만이 해결책
- 확장 메서드
  - static 클래스 안에 선언되어야 함
  - static 메서드여야 함
  - 첫 번째 매개변수는 this 한정자를 사용하여 확장할 형식을 지정
- add folder (Extensions)
- add class file (Extensions/StringExtensions.cs)

```csharp
public static class StringExtensions
{
    public static bool IsNumeric(this string s)
    {
        return long.TryParse(s, out long result);
    }

    public static bool IsDateTime(this string s)
    {
        if (String.IsNullOrEmpty(s))
            return false;

        return DateTime.TryParse(s, out DateTime result);
    }
}
```

- add class file (Extensions/DateTimeExtensions.cs)

```csharp
public static class DateTimeExtensions
{
    public static DateTime FirstDateOfMonth(this DateTime date)
    {
        return new DateTime(date.Year, date.Month, 1);
    }

    public static DateTime LastDateOfMonth(this DateTime date)
    {
        return new DateTime(date.Year, date.Month, DateTime.DaysInMonth(date.Year, date.Month));
    }
}
```

#### <a href="https://www.youtube.com/watch?v=nbMpipnMJ50" target="_blank">5. Email Manager</a>

- 환경변수 사용을 위해 YoungLIbTest 프로젝트에 App.config 편집 (라이브러리 프로젝트는 App.config가 없음)
- appSettings 항목 추가

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
 <appSettings>
  <add key="SMTPSender" value="do_not_reply@test.com" />
  <add key="SMTPHost" value="smtp.com" />
  <add key="SMTPId" value="id" />
  <add key="SMTPPw" value="password" />
  <add key="SMTPPort" value="2525" />
 </appSettings>
</configuration>
```

- add class file (Tools/EmailManager.cs)
- ConfigurationManager를 사용하기 위해서는 System.Configuration 어셈블리를 참조에 추가해야 함
- 참조 추가 -> 어셈블리 -> 프레임워크 -> System.Configuration 체크

```csharp
using System.Net;
using System.Net.Mail;
using System.Configuration;

public class EmailManager
{
    public static void Send(string to, string subject, string content)
    {
        //App.config에 해당 항목이 없으면 null 반환
        string sender = ConfigurationManager.AppSettings["SMTPSender"];
        string smtpHost = ConfigurationManager.AppSettings["SMTPHost"];
        string smtpId = ConfigurationManager.AppSettings["SMTPId"];
        string smtpPw = ConfigurationManager.AppSettings["SMTPPw"];
        int.TryParse(ConfigurationManager.AppSettings["SMTPPort"], out int smtpPort);
        smtpPort = (smtpPort == 0)? 25 : smtpPort;

        var mailMsg = new MailMessage();
        mailMsg.From = new MailAddress(sender);
        mailMsg.To.Add(to);
        mailMsg.Subject = subject;
        mailMsg.IsBodyHtml = true;
        mailMsg.Body = content;
        mailMsg.Priority = MailPriority.Normal;
        
        var smtpClient = new SmtpClient();
        smtpClient.Credentials = new NetworkCredential(smtpId, smtpPw);
        smtpClient.Host = smtpHost;
        smtpClient.Port = smtpPort;
        smtpClient.Send(mailMsg);
    }
}
```

#### <a href="https://www.youtube.com/watch?v=tn_qFcH75y4" target="_blank">6. Email Manager의 확장</a>

- Send 메서드 오버로딩

```csharp
public class EmailManager
{
    private MailMessage _mailMessage;
    private SmtpClient _smtpClient;

    public string From
    {
        get { return (_mailMessage.From == null) ? String.Empty : _mailMessage.From.Address; }
        set { _mailMessage.From = new MailAddress(value); }
    }

    public MailAddressCollection To
    {
        get { return _mailMessage.To; }
    }

    public string Subject
    {
        get { return _mailMessage.Subject; }
        set { _mailMessage.Subject = value; }
    }

    public string Body
    {
        get { return _mailMessage.Body; }
        set { _mailMessage.Body = value; }
    }

    public EmailManager(string host, int port, string id, string password)
    {
        _smtpClient = new SmtpClient(host, port);
        _smtpClient.Credentials = new NetworkCredential(id, password);

        _mailMessage = new MailMessage();
        _mailMessage.IsBodyHtml = true;
        _mailMessage.Priority = MailPriority.Normal;
    }

    public void Send()
    {
        _smtpClient.Send(_mailMessage);
    }

    //App.config에서 읽어들임
    #region Static Methods
    public static void Send(string smtpSender, string to, string subject, string content, string cc, string bcc)
    {
        //null check
        if (String.IsNullOrEmpty(smtpSender))
            throw new ArgumentNullException("smtpSender is empty");
        if (String.IsNullOrEmpty(to))
            throw new ArgumentNullException("to is empty");
        if (String.IsNullOrEmpty(subject))
            throw new ArgumentNullException("subject is empty");
        if (String.IsNullOrEmpty(content))
            throw new ArgumentNullException("content is empty");
        
        //App.config에 해당 항목이 없으면 null 반환
        string smtpHost = ConfigurationManager.AppSettings["SMTPHost"];
        string smtpId = ConfigurationManager.AppSettings["SMTPId"];
        string smtpPw = ConfigurationManager.AppSettings["SMTPPw"];
        int.TryParse(ConfigurationManager.AppSettings["SMTPPort"], out int smtpPort);
        smtpPort = (smtpPort == 0) ? 25 : smtpPort;
        
        var mailMsg = new MailMessage();
        mailMsg.From = new MailAddress(smtpSender);
        mailMsg.To.Add(to);
        mailMsg.Subject = subject;
        mailMsg.IsBodyHtml = true;
        mailMsg.Body = content;
        if (!String.IsNullOrEmpty(cc)) mailMsg.CC.Add(cc);
        if (!String.IsNullOrEmpty(bcc)) mailMsg.Bcc.Add(bcc);
        mailMsg.Priority = MailPriority.Normal;
        
        var smtpClient = new SmtpClient();
        smtpClient.Credentials = new NetworkCredential(smtpId, smtpPw);
        smtpClient.Host = smtpHost;
        smtpClient.Port = smtpPort;
        smtpClient.Send(mailMsg);
    }
    
    public static void Send(string smtpSender, string to, string subject, string content)
    {
        Send(smtpSender, to, subject, content, null, null);
    }
    
    public static void Send(string to, string subject, string content)
    {
        string smtpSender = ConfigurationManager.AppSettings["SMTPSender"];
        Send(smtpSender, to, subject, content);
    }
    #endregion
}
```

#### 7. Test Code

- App.config는 Solution_YoungLIb\YoungLIbTest\bin\Debug\YoungLIbTest.exe.config로 생성되어 있음
- id, password는 encrypt 해서 저장한 뒤, 프로그램에서 decrypt 후 사용하도록 하면 더 안전

```csharp
static void Main(string[] args)
{
    //var log = new LogManager();
    //log.WriteLine("test");

    //string s = null;
    //Console.WriteLine(s.IsNumeric());
    
    //EmailManager.Send("ext2fs@naver", "subject", "content.......");
    
    EmailManager email = new EmailManager("smtp.com", 25, "id", "password");
    email.From = "sender@test.com";
    email.To.Add("reciever@naver.com");
    email.Subject = "subject";
    email.Body = "content";
    email.Send();
}
```
