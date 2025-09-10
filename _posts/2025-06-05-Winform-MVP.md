---
layout: single
title: "Winform MVP with CRUD"
categories: WinForm
tags: [Csharp, WinForm, MVP]
---

### Modern UI by WinForm

- <a href="https://www.youtube.com/watch?v=WSBy_Ypgk38" target="_blank">Youtube-1/3</a>
- <a href="https://www.youtube.com/watch?v=wa_QqWYgQYY&t=8s" target="_blank">Youtube-2/3</a>
- <a href="https://www.youtube.com/watch?v=FeOOnMyjmmk&t=39s" target="_blank">Youtube-3/3</a>
- <a href="https://github.com/ext3fs/DevExpress_Tutorial_Solution/tree/main/Winform_MVP" target="_blank">GitHub</a>

#### 1. Database

```sql
create database VeterinaryDb 
go
use VeterinaryDb
go
create table Pet
(
  Pet_Id int identity (100000,1) primary key,
  Pet_Name nvarchar (50) not null,
  Pet_Type nvarchar (50) not null,
  Pet_Colour nvarchar (50) not null,  
)
go
insert into Pet values('Buttons', 'Dog', 'White')
insert into Pet values('Coda', 'Cat', 'Multicolor')
insert into Pet values('Merlin', 'Parrot', 'Green-Yellow')
insert into Pet values('Nina', 'Turtle', 'Dark Gray')
insert into Pet values('Domino', 'Rabbit', 'White')
insert into Pet values('Luna', 'Hamster', 'Orange')
insert into Pet values('Lucy', 'Monkey', 'Brown')
insert into Pet values('Daysi', 'Horse', 'White')
insert into Pet values('Zoe', 'Snake', 'Yellow white')
insert into Pet values('Max', 'Budgie', 'Yellow')
insert into Pet values('Charlie', 'Mouse', 'White')
insert into Pet values('Rocky', 'Squirrel', 'Brown-Orange')
insert into Pet values('Leo', 'Dog', 'White-Black')
insert into Pet values('Loki', 'Cat', 'Black')
insert into Pet values('Jasper', 'Dog', 'Silver')
go
```

#### 2. Models

- create folder : Models, Views, Presenters

```csharp
// PetModel.cs
public class PetModel
{
    [DisplayName("Pet ID")]
    public int Id { get; set; }

    [DisplayName("Pet Name")]
    [Required(ErrorMessage = "Pet name is requerid")] // not null
    [StringLength(50, MinimumLength = 3, ErrorMessage = "Pet name must be between 3 and 50 characters")]
    public int Name { get; set; }
    
    [DisplayName("Pet Type")]
    [Required(ErrorMessage = "Pet type is requerid")]
    [StringLength(50, MinimumLength = 3, ErrorMessage = "Pet type must be between 3 and 50 characters")]
    public int Type { get; set; }
    
    [DisplayName("Pet Colour")]
    [Required(ErrorMessage = "Pet colour is requerid")]
    [StringLength(50, MinimumLength = 3, ErrorMessage = "Pet colour must be between 3 and 50 characters")]
    public int Colour { get; set; }
}
```

```csharp
// IPetRepository.cs
public interface IPetRepository
{
    void Add(PetModel petModel);
    void Edit(PetModel petModel);
    void Delete(int id);
    IEnumerable<PetModel> GetAll();
    IEnumerable<PetModel> GetByValue(string value);//Searchs
}
```

#### 3. Views

```csharp
// IPetView.cs
public interface IPetView
{
    //Properties - Fields
    string PetId { get; set; }
    string PetName { get; set; }
    string PetType { get; set; }
    string PetColour { get; set; }

    string SearchValue { get; set; }
    bool IsEdit { get; set; }
    bool IsSuccessful { get; set; }
    string Message { get; set; }

    //Events
    event EventHandler SearchEvent;
    event EventHandler AddNewEvent;
    event EventHandler EditEvent;
    event EventHandler DeleteEvent;
    event EventHandler SaveEvent;
    event EventHandler CancelEvent;
    
    //Methods
    void SetPetListBindingSource(BindingSource petList);
    void Show();
}
```

- Add Form : PetView.cs
  - panel : Dock(Top), backColor(White)
  - label : Text("FETS"), FontSize(12)
  - TabControl : Dock(Fill)
  - TabPage1 : Text("Pet List")
    - LayoutControl : Dock(Fill)
    - TextEdit
    - SimpleButton : MaximumSize = Size와 같게하면 사이즈가 변하지 않음
    - Add Empty Space Item : Size Constraint -> Lock Width, minSize(151, 1)
    - DataGridView
  - TabPage2 : Text("Pet Detail")
    - LayoutControl : Dock(Fill)
    - TextEdit
    - Add Empty Space Item : Size Constraint -> Lock Width, minSize(151, 1)

#### 4. Presenters

```csharp
```
