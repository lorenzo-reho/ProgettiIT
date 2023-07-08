### 1. NET Framework WPF 

Windows Presentation Foundation (WPF) è un pacchetto/framework che consente di sfruttare i motori grafici dei sistemi moderni per visualizzare e progettare l'itnerfaccia grafica dei software.
Questo Framework consente di creare la User Interface tramite il linguaggio di markup XAML.

### 2. WPF & MVVM

Per sviluppare delle applicazioni sfruttando la tecnologia WPF è possibile usufruire di un pattern di progettazione chiamato __MVVM__

### 2.1 Model

I Model costituiscono la rappresentazione logica delle entità e si occupano di memorizzare ed elaborare i dati tramite metodi e attributi, accedere al database e tutto il resto delle operazioni logiche che il software deve performare.

Una classe Model implementa __INotifyPropertyChanged__ che fornisce metodi che consentono di notificare ai __ViewModel__ il cambiamento dello stato di determinate proprietà.

### 2.1 ViewModel

I ViewModel contengono dei metodi che controllano ciò che viene visualizzato all'interno delle View e gestiscono la logica di interazione con l'utente.

Anche i __ViewModel__ implementano __INotifyProperttyChanged__ che consente di notificare alla __View__ il cambiamento di determinate proprietà.

__IMPLEMENTAZIONE CLASSE VIEWMODEL:__

1) Creazione classe __ViewModelBase__ che implementa __INotifyPropertyChanged__ --> __OnPropertyChanged(...)__.
2) Creazione classe __ViewModelCommand__ che implementa __ICommand__.
3) La classe "__LoginViewModel__" implementa ViewModelBase e istanzia __ViewModelCommand__ per ogni operazione di interazione che l'utente puù effettuare e che il software deve gestire.

#### BaseViewModel

- __Gestione degli eventi__: https://www.csharptutorial.net/csharp-tutorial/csharp-events/

- __OnPropertyChanged__: Questo metodo va chiamato ogni volta che viene modificata una proprietà dal ViewModel
- __PropertyChangedEventArgs___: PropertyChangedEventArgs contiene le proprietà che devono essere passate alle classi subscriber (View)
- __this__: chi ha generato l'evento

```C#
    internal class BaseViewModel : INotifyPropertyChanged
    {
        public event PropertyChangedEventHandler PropertyChanged; // In C# gli eventi possono essere rappresentati come oggetti
        
        // Questo metodo va chiamato ogni volta che viene modificata una proprietà dal ViewModel
        // per notificare la modifica alla view
        public void OnPropertyChanged(string obj) {

            // Esame:
            // 1) PropertyChanged è l'handler dell'evento
            // 2) PropertyChangedEventArgs contiene le proprietà che devono essere passate alle classi subscriber (View)
            // 3) this è chi ha generato l'evento

            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(obj));
        }
    }

```

### CommandViewModel
Tutte le interazioni che modificano le proprietà della View o che consentono di interagire con i Models si basano su questa classe.

- __Action__ Type: Metodo che presenta un singolo parametro e non restituisce niente.
- __Predicate__ Type: Corrisponde ad un metodo che verifica un set di criteri e ritorna se sono soddisfati oppure no.
- __EventHandler CanExecuteChanged__: EventHandler che notifica se una determinata operazione ha modificato il suo stato di CanExecute.
- __CommandManager.RequerySuggested__: CommandManager.RequerySuggested è un event che consente di aggiungere o rimuovere un comando dal set di comandi eseguibili

```C#
internal class CommandViewModel : ICommand
{
	private Action<object> _execute;
	private Predicate<object> _canExecute;

	public CommandViewModel(Action<object> execute, Predicate<object> canExecute)
	{
		_execute = execute;
		_canExecute = canExecute;
	}

	// EventHandler che notifica se una determinata operazione ha modificato il suo stato di CanExecute.
	public event EventHandler CanExecuteChanged {
		// CommandManager.RequerySuggested è un event che consente di aggiungere o rimuovere
		// un comando dal set di comandi eseguibili
		add { CommandManager.RequerySuggested += value;  }
		remove { CommandManager.RequerySuggested -= value; }
	}

	public bool CanExecute(object parameter)
	{
		return _canExecute == null || _canExecute(parameter);
	}

	public void Execute(object parameter)
	{
		_execute(parameter);
	}
}

```

### Bind View and ViewModel

```xml
...
xmlns:ViewModel="clr-namespace:MedicalManagementSystem.ViewModel"
>

<Window.DataContext>
	<ViewModel:LoginViewModel />
</Window.DataContext>

...

<TextBox FontSize="13"
 Text="{Binding Username}"
 Height="35"
 VerticalContentAlignment="Center"
 BorderThickness="2"
 BorderBrush="#357ba1"
 Margin="0,5,0,10"
/>

```

### Window Resources & Visibility Binding

```xml

    <Window.Resources>
        <BooleanToVisibilityConverter x:Key="BooleanToVisibility"/>
    </Window.Resources>    
    
    <Window.Visibility>
        <Binding Path="IsViewVisible" Mode="TwoWay" Converter="{StaticResource BooleanToVisibility}"/>
    </Window.Visibility>
```


### Codice C# Creazione Custom UserControl con Binding

```C#
    public partial class BindablePasswordBox : UserControl
    {
        // SecurePassword è un campo di BindablePassword
        public static readonly DependencyProperty SecurePasswordProperty =
            DependencyProperty.Register("SecurePassword", typeof(SecureString), typeof(BindablePasswordBox));

        // Abbiamo accesso diretto a questo campo dalla View sottoforma di proprietà
        public SecureString SecurePassword
        {
            get { return (SecureString)GetValue(SecurePasswordProperty); }
            set
            {
                SetValue(SecurePasswordProperty, value);
            }
        }


        public BindablePasswordBox()
        {
            InitializeComponent();

            // aggiunta del metodo che deve essere eseguito quando la password cambia
            txtPassword.PasswordChanged += OnPasswordChanged;
        }

        // scatta al momento della modifica del valore
        public void OnPasswordChanged(object sender, RoutedEventArgs args)
        {
            // SecurePassword è la proprietà binded
            SecurePassword = txtPassword.SecurePassword;
        }
    }
```

### Creazione Custom Template XAML

```xml
    <Button Content="ACCEDI"
			Command="{Binding LoginCommand}"
			Margin="0,50,0,0"
			Background="#357ba1"
			Foreground="#fff"
			FontSize="15"
			FontWeight="Bold"
			Height="40">
	<Button.Template>
		<ControlTemplate TargetType="Button">
			<Border Background="#357ba1">
				<ContentPresenter VerticalAlignment="Center"
										  HorizontalAlignment="Center"/>
			</Border>
		</ControlTemplate>
	</Button.Template>
</Button>
```

### IsMouseOver, Trigger, TemplateBinding

- __Attenzione:__ 
	- _{TemplateBinding Background}_, e non mettere direttamente un Background su _Button_

```xml
<Button 
	x:Name="btnLogin"    
	Content="ACCEDI"
	Command="{Binding LoginCommand}"
	Margin="0,50,0,0"
	Foreground="#fff"
	FontSize="15"
	FontWeight="Bold"
	Height="40">

	<Button.Style>
		   <Style TargetType="Button">
				<Setter Property="Background" Value="#357ba1"/>
				<Style.Triggers>
					<Trigger Property="IsMouseOver" Value="True">
						<Setter Property="Background" Value="#000000"/>
					</Trigger>
				</Style.Triggers>
		</Style>
	</Button.Style>
	
	<Button.Template>
		<ControlTemplate TargetType="Button">
			<Border Background="{TemplateBinding Background}"
					CornerRadius="10">
				<ContentPresenter VerticalAlignment="Center"
								  HorizontalAlignment="Center"/>
				
			</Border>
		</ControlTemplate>
	</Button.Template>

</Button>
```

### Collegamento DependencyProperty Custom di un CustomControl con una proprietà di una entità all'interno del controllo stesso.

```xml
<customcontrols:IconButton TextButton="Home"/>
```

```xml
    <StackPanel Orientation="Horizontal"
                            Margin="10,0,0,0">
        <Image Width="32" Source="../Images/home.png"/>

        <Button
            x:Name="customBtn"
            Content="{Binding TextButton,RelativeSource={RelativeSource Mode=FindAncestor,AncestorType={x:Type UserControl}}}"
            Background="Transparent"
            Foreground="White"
            BorderThickness="0"
            FontWeight="Bold"
            FontSize="13"
            Margin="10,0,0,0"
        />
    </StackPanel>
```

### Visualizzazione di una View figlia all'interno di una View Madre


```xml
	...
    <Window.Resources>
        <DataTemplate DataType="{x:Type ViewModel:DashboardViewModel}">
            <local:DashboardView/>
        </DataTemplate>
    </Window.Resources>
	...
    <Grid>
       <ContentPresenter Content="{Binding CurrentViewModel}"/>
    </Grid>
```

__Content__ del __ContentPresenter__ può essere collegato a una qualsiasi _object_, è necessario però definire quale View visualizzare per quel particolare tipo di controllo. In questo caso ho collegato ContentPresenter ad un generico _CurrentViewModel_ che può variare sulla base dello stato dell'applicazione, se ad esempio il CurrentViewModel dovesse diventare un'istanza dell'oggetto DashboardViewModel, allora bisogna visualizzare la __DashboardView__.

- If there is a [DataTemplate](https://learn.microsoft.com/en-us/dotnet/api/system.windows.datatemplate?view=windowsdesktop-7.0) associated with the type of [Content](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.contentpresenter.content?view=windowsdesktop-7.0), the [ContentPresenter](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.contentpresenter?view=windowsdesktop-7.0) applies that [DataTemplate](https://learn.microsoft.com/en-us/dotnet/api/system.windows.datatemplate?view=windowsdesktop-7.0) to the [Content](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.contentpresenter.content?view=windowsdesktop-7.0) property and the resulting [UIElement](https://learn.microsoft.com/en-us/dotnet/api/system.windows.uielement?view=windowsdesktop-7.0) and its child elements, if any, are displayed.

### Personalizzazione TextBox aggiungendo CornerRadius ecc...

```xml
	<Style x:Key="SearchField" TargetType="{x:Type TextBox}">

		<Style.Setters>
			<Setter Property="Background" Value="#fff"/>
			<Setter Property="Template">
				<Setter.Value>
					<ControlTemplate TargetType="{x:Type TextBox}">
						<Border Background="{TemplateBinding Background}" CornerRadius="10">
							<ScrollViewer x:Name="PART_ContentHost"/>
						</Border>
					</ControlTemplate>
				</Setter.Value>
			</Setter>
		</Style.Setters>
		
	</Style>
```

The following table lists the named parts for the [TextBox](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.textbox) control.
- __PART_ContentHost__: A visual element that can contain a [FrameworkElement](https://learn.microsoft.com/en-us/dotnet/api/system.windows.frameworkelement). The text of the [TextBox](https://learn.microsoft.com/en-us/dotnet/api/system.windows.controls.textbox) is displayed in this element.

### Grid Column a scomparsa e animazione togglebutton

```xml
<ColumnDefinition>
  <ColumnDefinition.Style>
    <Style TargetType="ColumnDefinition">
      <Setter Property="Width" Value="*" />
        <Style.Triggers>
          <DataTrigger Binding="{Binding IsColumnVisible}" Value="False">
            <Setter Property="Width" Value="0" />
          </DataTrigger>
        </Style.Triggers>
    </Style>
  </ColumnDefinition.Style>
</ColumnDefinition>
```

```xml
<ToggleButton Width="40" Margin="10,0,0,0" Command="{Binding SearchFilterCommand}">
                        <Image Source="../Images/search1.png" Width="30" Height="30"/>

                        <ToggleButton.Triggers>
                            <EventTrigger RoutedEvent="ToggleButton.Unchecked">
                                <BeginStoryboard>
                                    <Storyboard x:Name="HideStackPanel">
                                        <DoubleAnimation    
                                                Storyboard.TargetName="searchFilterGrid"
                                                Storyboard.TargetProperty="Width"
                                                BeginTime="0:0:0"
                                                From="140" To="0"
                                                Duration="0:0:0.2">
                                        </DoubleAnimation>
                                    </Storyboard>
                                </BeginStoryboard>
                            </EventTrigger>
                            <EventTrigger RoutedEvent="ToggleButton.Checked">
                                <BeginStoryboard>
                                    <Storyboard x:Name="ShowStackPanel">
                                        <DoubleAnimation
                                                Storyboard.TargetName="searchFilterGrid"
                                                Storyboard.TargetProperty="Width"
                                                BeginTime="0:0:0"
                                                From="0" To="140"
                                                Duration="0:0:0.3">
                                        </DoubleAnimation>
                                    </Storyboard>
                                </BeginStoryboard>
                            </EventTrigger>
                        </ToggleButton.Triggers>
                    </ToggleButton>
```

### ComboBox editabili

```xml
<Window x:Class="LearnWPF.EditableComboBox.Window1"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    Title="LearnWPF.EditableComboBox" Height="300" Width="300"
    >
  <Window.Resources>
    <XmlDataProvider x:Key="items" XPath="//item">
      <x:XData>
        <items >
          <item>01</item>
          <item>02</item>
          <item>03</item>
        </items>
      </x:XData>
    </XmlDataProvider>
  </Window.Resources>
    <Grid>
      <ComboBox IsEditable="True" DataContext="{StaticResource items}"
                ItemsSource="{Binding}"/>
    </Grid>
</Window>
  <ComboBox Margin="10,10,0,13" Name="ComboBox1" HorizontalAlignment="Left" VerticalAlignment="Top" Width="194" Height="30">  
      <ComboBoxItem Content="Coffie"></ComboBoxItem>  
      <ComboBoxItem Content="Tea"></ComboBoxItem>  
      <ComboBoxItem Content="Orange Juice"></ComboBoxItem>  
      <ComboBoxItem Content="Milk"></ComboBoxItem>  
      <ComboBoxItem Content="Iced Tea"></ComboBoxItem>  
      <ComboBoxItem Content="Mango Shake"></ComboBoxItem>  
</ComboBox>
```


### Spiegazione sorgente relativa

https://stackoverflow.com/questions/84278/how-do-i-use-wpf-bindings-with-relativesource

### Thread e store delle informazioni sull'utente loggato

- https://learn.microsoft.com/en-us/dotnet/api/system.threading.thread.currentprincipal?view=net-7.0#system-threading-thread-currentprincipal

```C#
using System;
using System.Security;
using System.Security.Permissions;
using System.Security.Principal;
using System.Threading;

class Principal
{
    static void Main()
    {
        string[] rolesArray = {"managers", "executives"};
        try
        {
            // Set the principal to a new generic principal.
            Thread.CurrentPrincipal = 
                new GenericPrincipal(new GenericIdentity(
                "Bob", "Passport"), rolesArray);
        }
        catch(SecurityException secureException)
        {
            Console.WriteLine("{0}: Permission to set Principal " +
                "is denied.", secureException.GetType().Name);
        }

        IPrincipal threadPrincipal = Thread.CurrentPrincipal;
        Console.WriteLine("Name: {0}\nIsAuthenticated: {1}" +
            "\nAuthenticationType: {2}", 
            threadPrincipal.Identity.Name, 
            threadPrincipal.Identity.IsAuthenticated,
            threadPrincipal.Identity.AuthenticationType);
    }
}
```

### Connessione al Database SQL SERVER

```C#
internal class DBRepository
{

	private String connectionString;

	public DBRepository() {
		connectionString = "Data source=HOST-MACHINE;Initial Catalog=MedicalMS;User ID=lorenzo;Password=password";
	}

	protected SqlConnection GetConnection() {
		return new SqlConnection(connectionString);
	}

}
```

### SELECT QUERY & PREVENZIONE SQL INJECTION

```C#
        public Tuple<String, int> AutenticazioneUtente(NetworkCredential networkCredential)
        {

            /*
                Il motivo usingdell'istruzione è garantire che l'oggetto venga eliminato non appena esce dall'ambito e non richiede codice esplicito per garantire che ciò accada.
             */
            String queryString = "SELECT CodiceFiscale, email, username, Ruolo, password FROM Utenti WHERE (username=@uname OR email=@uname) AND password=@password";
            String CodiceFiscale = null;
            int role = -1;


            using (SqlConnection connection = GetConnection()) {
                connection.Open();
                SqlCommand command = new SqlCommand(queryString, connection);
                
                // Metodi di prevenzione per SQL INJECTION
                
                command.Parameters.Add("@uname", System.Data.SqlDbType.VarChar);
                command.Parameters.Add("@password", System.Data.SqlDbType.Binary); // la password non viene salvata come stringa ma come binary
                command.Parameters["@uname"].Value = networkCredential.UserName;
                command.Parameters["@password"].Value = CryptoRepository.Hash(networkCredential.Password);

                using (SqlDataReader reader = command.ExecuteReader()) {
                    while (reader.Read()) {
                        CodiceFiscale = reader.GetString(0);
                        role = reader.GetInt32(3);
                    }
                }
                
            }

            return Tuple.Create(CodiceFiscale, role);
        }
```

### ObservableCollection

Rappresenta una raccolta dati dinamica che fornisce notifiche quando gli elementi vengono aggiunti o rimossi o quando l'intero elenco viene aggiornato.

```C#
public class ObservableCollection<T> : System.Collections.ObjectModel.Collection<T>, System.Collections.Specialized.INotifyCollectionChanged, System.ComponentModel.INotifyPropertyChanged
```

### DataGrid Dinamica



```HTML
<DataGrid ItemsSource="{Binding Users, UpdateSourceTrigger=PropertyChanged}" ColumnWidth="*" IsReadOnly="True" AutoGenerateColumns="False">
	<DataGrid.Columns>
		<DataGridTextColumn Header="CodiceFiscale" Binding="{Binding CodiceFiscale}"/>
		<DataGridTextColumn Header="Nome" Binding="{Binding Nome}"/>
		<DataGridTextColumn Header="Cognome" Binding="{Binding Cognome}"/>
		<DataGridTextColumn Header="Email" Binding="{Binding Email}"/>
	</DataGrid.Columns>
	
</DataGrid>
```

```C#

public ObservableCollection<BaseUserModel> Users { get; set; }

public UserManageModel() {

	Users = new ObservableCollection<BaseUserModel>
	{
		new BaseUserModel { CodiceFiscale = "ABC123", Nome = "Lorenzo", Cognome = "Reho", Email = "lorenzoreho852@gmail.com" }
	};

}

```

__Esempi di applicazioni:__

```C#
// attributi che vengono visualizzati/importati dalla View
private string _username;
private SecureString _password;
...

// Metodi di accesso e modifica degli attributi
public string Username{
	
	get{
		return _username;	
	}

	set{
		_username = value;
		OnPropertyChanged(nameof(Username)); // serve a notificare la modifica di Username
	}

}

...

```

- Creazione degli oggetti __ICommand__:
```C#
public ICommand LoginCommand { get; }
public ICommand RecoverPasswordCommand { get; }
public ICommand ShowPasswordCommand { get; }
...
```

- Successivamente bisogna instanziare questi oggetti tramite la classe __LoginViewModel__ all'interno del costruttore.

```C#
public LoginViewModel(){
	LoginCommand = new ViewModelCommand(ExecuteLoginCommand, canExecuteLoginCommand);
}

public bool ExecuteLoginCommand(object obj){
	// viene definita la logica dietro l'esecuzione del comando di login
}

public bool CanExecuteLoginCommand(object obj){
	// si definisce se è possibile eseguire il comando
}
```


