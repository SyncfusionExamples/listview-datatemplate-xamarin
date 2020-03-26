# How to access a named ListView inside a XAML DataTemplate in Xamarin.Forms (SfListView)?

You can access the named [SfListView](https://help.syncfusion.com/xamarin/listview/overview?) defined inside [DataTemplate](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/app-fundamentals/templates/data-templates/) of [PopupLayout](https://help.syncfusion.com/xamarin/listview/overview?) by using [Behavior](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/app-fundamentals/behaviors/creating).

You can refer the following article for more reference

https://www.syncfusion.com/kb/11287/how-to-access-a-named-listview-inside-a-xaml-datatemplate-in-xamarin-forms-sflistview

**XAML**

In PopupLayout’s [PopupView](https://help.syncfusion.com/cr/xamarin/Syncfusion.SfPopupLayout.XForms~Syncfusion.XForms.PopupLayout.PopupView.html?), behaviour added to parent of ListView.

``` xml
<sfPopup:SfPopupLayout x:Name="popupLayout">
    <sfPopup:SfPopupLayout.PopupView>
        <sfPopup:PopupView >
            <sfPopup:PopupView.ContentTemplate>
                <DataTemplate>
                    <Grid>
                        <Grid.Behaviors>
                            <local:GridBehavior/>
                        </Grid.Behaviors>                            
                        <Button Text="Find ListView" x:Name="listviewButton" />
                        <sfListView:SfListView   x:Name="listView"  
                                                    ItemsSource="{Binding Items}" >
                            <sfListView:SfListView.ItemTemplate>
                                <DataTemplate>
                                     …
                                </DataTemplate>
                            </sfListView:SfListView.ItemTemplate>
                        </sfListView:SfListView>
                    </Grid>
                </DataTemplate>
            </sfPopup:PopupView.ContentTemplate>
        </sfPopup:PopupView>
    </sfPopup:SfPopupLayout.PopupView>
    <sfPopup:SfPopupLayout.Content>
        <Grid>
            <Button x:Name="ShowPopup" Text="Bring Popup"/>
        </Grid>
    </sfPopup:SfPopupLayout.Content>
</sfPopup:SfPopupLayout>
```
**C#**

In ChildAdded event you will get the instance of ListView.

``` c#
public class GridBehavior : Behavior<Grid>
{
    Grid grid;
    SfListView listView;
    protected override void OnAttachedTo(BindableObject bindable)
    {
        grid = bindable as Grid;
        grid.ChildAdded += Grid_ChildAdded;
    }
    //Method 1 : Get SfListView reference using Grid.ChildAdded Event
    private void Grid_ChildAdded(object sender, ElementEventArgs e)
    {
        if (e.Element is SfListView)
        {
            listView = e.Element as SfListView;
            listView.RefreshView();
        }
    }
    protected override void OnDetachingFrom(BindableObject bindable)
    {
        grid.ChildAdded -= Grid_ChildAdded;
        listView = null;
        grid = null;
        base.OnDetachingFrom(bindable);
    }
}
```
**C#**

You can also get the ListView using [FindByName](https://docs.microsoft.com/en-us/dotnet/api/xamarin.forms.element.findbyname?view=xamarin-forms) method from Parent element.
``` c#
public class GridBehavior : Behavior<Grid>
{
    Grid grid;
    SfListView listView;
    Button button;
    protected override void OnAttachedTo(BindableObject bindable)
    {
        grid = bindable as Grid;
        grid.ChildAdded += Grid_ChildAdded;
    }
    private void Grid_ChildAdded(object sender, ElementEventArgs e)
    {
        if (e.Element is Button)
        {
            button = e.Element as Button;
            button.Clicked += Button_Clicked;
        }
    }
    //Method 2 : Get SfListView reference using FindByName
    private void Button_Clicked(object sender, EventArgs e)
    {
        listView = grid.FindByName<SfListView>("listView");
        App.Current.MainPage.DisplayAlert("Information", "ListView instance obtained", "Ok");
        listView.ItemTapped += ListView_ItemTapped;
    }
 
    private void ListView_ItemTapped(object sender, Syncfusion.ListView.XForms.ItemTappedEventArgs e)
    {
        App.Current.MainPage.DisplayAlert("Information", "ListView ItemTapped", "Ok");
    }
 
    protected override void OnDetachingFrom(BindableObject bindable)
    {
        button.Clicked -= Button_Clicked;
        grid.ChildAdded -= Grid_ChildAdded;
        listView.ItemTapped -= ListView_ItemTapped;
        listView = null;
        button = null;
        grid = null;
        base.OnDetachingFrom(bindable);
    }
}
```
