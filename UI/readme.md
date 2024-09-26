## Opret ny skærm

En skærm er en klasse, som tegner en titel og noget indhold i konsollen. Det gør den ved hjælp af en Draw() metode, hvor det står brugeren frit for at bruge dotnets indbyggede Console metoder til at tegne på skærmen (WriteLine, SetCursorPosition osv). På den måde giver en skærm store friheder.
Hvis brugeren trykker på en tast vil skærmklassen tegne skærmen igen.

1. Opret en tom klasse der arver fra TECHCOOL.UI.Screeen

  ``` cs
  using TECHCOOL.UI;
  public class MyFirstScreen : Screen
  {
      public override string Title { get; set; } = "My first screen"; 
      protected override void Draw()
      {
          Console.WriteLine("My first screen!");
          Quit();
      }
  }
  ```

2. Vi bruger skærmen ved at bruge den statiske metode Display() på Screen klassen.
   
  ``` cs
  public class Program 
  {
  public static void Main(string[] args)
  {
      MyFirstScreen firstScreen = new MyFirstScreen();
      Screen.Display(firstScreen);
  }
  }
  ```
   
3. Det munder ud i følgende output i konsollen
   
  ```
  ===My first screen===
  My first screen! 
  ```

## Opret en liste

I dette eksempel vises oprettelse af en liste i TECHCOOL. Det første vi skal bruge er en dataklasse. Derefter instantierer vi en liste og fortæller den hvilke properties på dataklassen, som vi er interesserede i at vise i listen.

1. Opret dataklassen. Denne dataklasse indeholder en titel, en prioritet og en tilstand, som afgør om en todo er i gang
   
  ``` cs
  public class Todo
  {
    public enum TodoState { Todo, Started, Done }
    public string Title {get;set;} = "";
    public int Priority {get;set;}
    public TodoState State {get;set;}
    public Todo(string title, int priority=1) 
    {
      Title = title;
      Priority = priority;
    }
    public Todo() : this("<title>", 0) { }
  }
  ```

2. Opret en ny skærm: TodoListScreen.cs
   
  ``` cs
  public class TodoListScreen : Screen
  {
   public override string Title { get; set; } = "List of tasks to do"; 
   protected override void Draw()
   {
     //Gonna draw a list page here
   }
  }
  ```

3. I Draw metoden laver vi en instans af en ListPage. Vores ListPage skal vide hvilken type klasse, som den skal vise. Det fortæller vi den ved at bruge en generic `ListPage<Todo> listPage = new();`

4. Tilføj noget data til listen
   
  ``` cs
  //...
  listPage.Add(new Todo("Buy milk"));
  listPage.Add(new Todo("Walk the dog", 2));
  listPage.Add(new Todo("Clean toilet"));
  ```

5. Fortæl vores ListPage hvilke kolonner den skal vise. 
   
  ``` cs
  //...
  listPage.AddColumn("Todo", "Title");
  ```
   
   Her konfigureres listen til at vise én kolonne. Øverst i listen vil der stå "Todo" som angivet i det første parameter i AddColumn() og under overskriften vil propertyen _Title_ fra Todo klassen blive vist.

6. Tegn listen
   Listen kan blive tegnet på skærmen gennem metoden Draw(). 
   
  ``` cs
  ListPage<Todo> listPage = new ListPage<Todo>();
  listPage.Add(new Todo("Buy milk"));
  listPage.Add(new Todo("Walk the dog", 2));
  listPage.Add(new Todo("Clean toilet"));

  listPage.AddColumn("Todo", "Title"); //Add column to the list

  listPage.Draw(); //Draw the screen
  Console.ReadLine();
  Quit();
  ``` 

7. Brug den nye TodoListScreen sammen med Screen.Display()

`Screen.Display( new TodoListScreen() );`

Output

```
===List of tasks to do===
-----------------

| Todo         |
| ------------ |
| Buy milk     |
| Walk the dog |
| Clean toilet |

-----------------

```

8. Prøv selv at tilføje kolonnerne for propertierne **Priority** og **State** i Todo klassen
9. Vælg en Todo fra listen ved at bruge metoden **Select()** på listPage i stedet for **Draw()**

``` cs
//...
Todo selected = listPage.Select(); //Select todo from the list
Console.WriteLine("You selected: "+ selected.Title);
```

Dette gør listen interaktiv og lader brugeren vælge en fra listen.

10. Færdigt eksempel
    
  ```cs
  public class TodoListScreen : Screen
  {
      public override string Title { get; set; } = "List of tasks to do";
      protected override void Draw()
      {
          ListPage<Todo> listPage = new();
          listPage.Add(new Todo("Buy milk"));
          listPage.Add(new Todo("Walk the dog", 2));
          listPage.Add(new Todo("Clean toilet"));

          listPage.AddColumn("Todo", "Title");
          // listPage.Draw();

          Todo selected = listPage.Select();
          Console.WriteLine("You selected: " + selected.Title);
          Console.ReadLine();

          Quit();
      }
  } 
  ```

## Redigering af objekter

Denne guide viser hvordan du kan redigere et objekt ved hjælp af TECHCOOL konsol UI. Guiden tager udgangspunkt i samme klasse som guiden "Opret en liste", nemlig Todo klassen

  ``` cs
  public class Todo
  {
    public enum TodoState { Todo, Started, Done }
    public string Title {get;set;} = "";
    public int Priority {get;set;}
    public TodoState State {get;set;}
    public Todo(string title, int priority=1) 
    {
      Title = title;
      Priority = priority;
    }
    public Todo() : this("<title>", 0) { }
  }
  ```
 

1. Opret en ny skærm: EditTodoScreen

  ``` cs
  public class EditTodoScreen : Screen
  {
      public override string Title { get; set; } = "Edit todo"; 
      protected override void Draw()
      {
          Todo todo = new Todo("New todo");

          //Gonna edit a todo

      }
  }
  ```

2. Opret en instans af TECHCOOL.UI.Form og put en TextBox i den.

  ``` cs
  //...
  //Create instance
  Form<Todo> editor = new ();

  //Add a textbox
  editor.TextBox("Todo name", "Title");

  //Draw the editor
  editor.Edit(todo);
  ```

Der er forskellige kontroller, som kan sættes op i en editor. Kontrollerne er afhængige af propertiens datatype:

* TextBox til strings
* SelectBox til flere valg (f.eks. enums)
* IntBox til int
* DoubleBox til double
  Som default kan TextBox bruges 
3. Færdigt eksempel
   
  ```cs
  public class EditTodoScreen : Screen
  {
      public override string Title { get; set; } = "Edit todo"; 
      protected override void Draw()
      {
          Todo todo = new Todo("New todo");

          Form<Todo> editor = new ();
          editor.TextBox("Todo", "Title");
          editor.IntBox("Importance", "Priority");
          editor.SelectBox("Progress", "State");
          editor.AddOption("Progress", "Not started", Todo.TodoState.Todo);
          editor.AddOption("Progress", "Begun", Todo.TodoState.Started);
          editor.AddOption("Progress", "Complete", Todo.TodoState.Done);
          editor.Edit(todo);

          Clear();
          Console.WriteLine($"Todo {todo.Title} is {todo.State}");
      }
  }
  ```

## Tilpasning af farver

Ønsker man at tilpasse flere skærme, så de f.eks. få et bestemt farve-tema, kan man lave en fælles basis-klasse, som skærmene arver fra. Prøv at tilføje følgende klasse til dit projekt, og lad dine øvrige skærme nedarve fra ColorScreen i stedet for blot Screen.

  ``` cs
  public abstract class ColorScreen : Screen
  {
      public ColorScreen() : base()
      {
          DefaultForeground = ConsoleColor.Yellow;
          DefaultBackground = ConsoleColor.Blue;
          FocusForeground = ConsoleColor.Green;
          FocusBackground = ConsoleColor.Cyan;
          FieldForeground = ConsoleColor.White;
          FieldBackground = ConsoleColor.DarkGray;
      }
  }
  ```

### Tilføj keybindings

Det er muligt på en ListPage at tilføje keybindings, som vil virke når vi kalder "Select()", udover PIL-op/ned ENTER.

  ``` cs
  listPage.AddKey(ConsoleKey.F12, ShowHelp);
  Console.WriteLine("Press F12 to show help");
  ```

Ovenståede tilføjer en keybinding til F12 og kalder funktionen ShowHelp, når brugeren trykker F12. For at få det til at virke, skal vi også tilføje den funktion som bliver kaldt.

  ``` cs
  void ShowHelp(Todo _)
  {
          Screen.Display(new HelpScreen());
  }
  ```

I ovenstående eksempel er vi ligeglade med hvilket Todo item listen stod på da vi trykkede F12. Derfor giver vi blot argumentet navnet "_". Hvis vi ønsker at bruge det enkelte item, kan vi give det et navn og bruge det i funktionen.

## Lav en menu

Menuer er en samling af skærme (Screens), som den lader brugeren vælge imellem. Vi tager udgangspunkt i de tre skærme vi har lavet i denne guide.

* MyFirstScreen

* TodoListScreen

* EditTodoScreen
1. Opret en ny skærm: MyMenuScreen
   
  ``` cs
  public class MyMenuScreen : Screen
  {
   public override string Title { get; set; } = "Menu Example"; 
   protected override void Draw()
   {
       //Gonna draw a menu here!
   }
  }
  ```

2. Opret en Menu instans og tilføj skærme til den
   
  ``` cs
  //...
  Menu menu = new Menu();
  //Add our screens
  menu.Add(new MyFirstScreen());
  menu.Add(new TodoListScreen());
  menu.Add(new EditTodoScreen());
  menu.Start(this); //Start the menu letting the user pick a menu item:
  ```

3. Brug nu menuen i din main metode:
   
  ``` cs
  public static void Main(string[] args)
  {
   Screen.Display(new MyMenuScreen());
  }
  ```
