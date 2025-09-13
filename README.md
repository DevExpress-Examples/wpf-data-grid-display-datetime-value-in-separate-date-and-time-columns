<!-- default badges list -->
![](https://img.shields.io/endpoint?url=https://codecentral.devexpress.com/api/v1/VersionRange/567311327/24.2.1%2B)
[![](https://img.shields.io/badge/Open_in_DevExpress_Support_Center-FF7200?style=flat-square&logo=DevExpress&logoColor=white)](https://supportcenter.devexpress.com/ticket/details/T1128471)
[![](https://img.shields.io/badge/📖_How_to_use_DevExpress_Examples-e9f6fc?style=flat-square)](https://docs.devexpress.com/GeneralInformation/403183)
[![](https://img.shields.io/badge/💬_Leave_Feedback-feecdd?style=flat-square)](#does-this-example-address-your-development-requirementsobjectives)
<!-- default badges end -->

# WPF Data Grid - Display the DateTime Value in Separate Date and Time Columns

Our [WPF Data Grid](https://docs.devexpress.com/WPF/6084/controls-and-libraries/data-grid) allows you to display separate columns for different portions of [DateTime](https://learn.microsoft.com/en-us/dotnet/api/system.datetime) objects. To display separate columns, you can use one of the following techniques:

* [Create Additional Date and Time Properties](#create-additional-date-and-time-properties)
* [Use Unbound Expressions](#use-unbound-expressions)
* [Use the CustomUnboundColumnData Command](#use-the-customunboundcolumndata-command)

## Create Additional Date and Time Properties

Modify the implementation of the data item class and add **Date** and **Time** properties. Use these properties as wrappers for the `DateTime` property:

```cs
public class Item : BindableBase {
    private DateTime dateTime;
    public DateTime DateTime {
        get => dateTime;
        set {
            dateTime = value;
            RaisePropertiesChanged();
        }
    }

    public DateTime Date {
        get => dateTime.Date;
        set {
            var time = dateTime.TimeOfDay;
            DateTime = value.Date + time;
            RaisePropertiesChanged();
        }
    }

    public DateTime Time {
        get => DateTime.Today.AddTicks(dateTime.TimeOfDay.Ticks);
        set {
            DateTime = dateTime.Date.AddTicks(value.TimeOfDay.Ticks);
            RaisePropertiesChanged();
        }
    }
}
```

### Files to Review

* [Item.cs](./CS/DateAndTimeColumns_Properties/Item.cs) (VB: [Item.vb](./VB/DateAndTimeColumns_Properties/Item.vb))
* [MainWindow.xaml](./CS/DateAndTimeColumns_Properties/MainWindow.xaml) (VB: [MainWindow.xaml](./VB/DateAndTimeColumns_Properties/MainWindow.xaml))


## Use Unbound Expressions

Define both **Date** and **Time** unbound columns and use [UnboundExpresssions](https://docs.devexpress.com/WPF/DevExpress.Xpf.Grid.ColumnBase.UnboundExpression) to display corresponding date and time values:

```xaml
<dxg:GridControl.Columns>
    <dxg:GridColumn FieldName="Date"
                    Header="Date"
                    UnboundType="DateTime"
                    UnboundExpression="GetDate([DateTime])"
                    AllowEditing="False"/>
    <dxg:GridColumn FieldName="Time" 
                    Header="Time"
                    UnboundType="DateTime" 
                    UnboundExpression="AddTicks(Today(), GetTimeOfDay([DateTime]))"
                    RoundDateTimeForColumnFilter="False"
                    AllowEditing="False">
        <dxg:GridColumn.EditSettings>
            <dxe:DateEditSettings DisplayFormat="T">
                <dxe:DateEditSettings.StyleSettings>
                    <dxe:DateEditTimePickerStyleSettings/>
                </dxe:DateEditSettings.StyleSettings>
            </dxe:DateEditSettings>
        </dxg:GridColumn.EditSettings>
    </dxg:GridColumn>
</dxg:GridControl.Columns>
```

If implemented in this manner, all unbound columns will be in a read-only state. To enable editing, use other techniques described herein ([Create Additional Date and Time Properties](#create-additional-date-and-time-properties) or [Use the CustomUnboundColumnData Command](#use-the-customunboundcolumndata-command)).


### Files to Review

* [Item.cs](./CS/DateAndTimeColumns_Unbound/Item.cs) (VB: [Item.vb](./VB/DateAndTimeColumns_Unbound/Item.vb))
* [MainWindow.xaml](./CS/DateAndTimeColumns_Unbound/MainWindow.xaml) (VB: [MainWindow.xaml](./VB/DateAndTimeColumns_Unbound/MainWindow.xaml))


## Use the CustomUnboundColumnData Command

Define both **Date** and **Time** unbound columns and use the [CustomUnboundColumnData](https://docs.devexpress.com/WPF/DevExpress.Xpf.Grid.GridControl.CustomUnboundColumnData) event or [CustomUnboundColumnDataCommand](https://docs.devexpress.com/WPF/DevExpress.Xpf.Grid.GridControl.CustomUnboundColumnDataCommand) to execute the conversion:

```cs
private void ColumnData(UnboundColumnRowArgs e) {
    if (e.IsGetData) {
        switch (e.FieldName) {
            case "Date":
                e.Value = Source[e.SourceIndex].DateTime.Date;
                break;
            case "Time":
                e.Value = DateTime.Today.AddTicks(Source[e.SourceIndex].DateTime.TimeOfDay.Ticks);
                break;
            default:
                break;
        }

        return;
    }

    if (e.IsSetData) {
        switch (e.FieldName) {
            case "Date":
                var time = Source[e.SourceIndex].DateTime.TimeOfDay;
                Source[e.SourceIndex].DateTime = ((DateTime)e.Value).Date + time;
                break;
            case "Time":
                Source[e.SourceIndex].DateTime = Source[e.SourceIndex].DateTime.Date
                      .AddTicks(((DateTime)e.Value).TimeOfDay.Ticks);
                break;
            default:
                break;
        }
    }
}
```


### Files to Review

* [Item.cs](./CS/DateAndTimeColumns_UnboundEditable/Item.cs) (VB: [Item.vb](./VB/DateAndTimeColumns_UnboundEditable/Item.vb))
* [MainWindow.xaml](./CS/DateAndTimeColumns_UnboundEditable/MainWindow.xaml) (VB: [MainWindow.xaml](./VB/DateAndTimeColumns_UnboundEditable/MainWindow.xaml))
* [MainViewModel.cs](./CS/DateAndTimeColumns_UnboundEditable/MainViewModel.cs) (VB: [MainViewModel.vb](./VB/DateAndTimeColumns_UnboundEditable/MainViewModel.vb))


## Documentation

* [Unbound Columns](https://docs.devexpress.com/WPF/6124/controls-and-libraries/data-grid/grid-view-data-layout/columns-and-card-fields/unbound-columns)
* [CustomUnboundColumnDataCommand](https://docs.devexpress.com/WPF/DevExpress.Xpf.Grid.GridControl.CustomUnboundColumnDataCommand)
* [UnboundExpresssions](https://docs.devexpress.com/WPF/DevExpress.Xpf.Grid.ColumnBase.UnboundExpression)


## More Examples

* [WPF Data Grid - Create Unbound Columns](https://github.com/DevExpress-Examples/how-to-create-unbound-columns-e1503)
<!-- feedback -->
## Does this example address your development requirements/objectives?

[<img src="https://www.devexpress.com/support/examples/i/yes-button.svg"/>](https://www.devexpress.com/support/examples/survey.xml?utm_source=github&utm_campaign=wpf-data-grid-display-datetime-value-in-separate-date-and-time-columns&~~~was_helpful=yes) [<img src="https://www.devexpress.com/support/examples/i/no-button.svg"/>](https://www.devexpress.com/support/examples/survey.xml?utm_source=github&utm_campaign=wpf-data-grid-display-datetime-value-in-separate-date-and-time-columns&~~~was_helpful=no)

(you will be redirected to DevExpress.com to submit your response)
<!-- feedback end -->
