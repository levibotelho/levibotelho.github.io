---
layout: post
title: [WPF] Auto-updating a sorted ListBox
category: Development
tags: [c#, xaml, wpf]
comments: true
share: true
---

I'm currently working on a project in WPF that integrates a ListBox of items whose order is specified by the user. Items are moved around by selecting them in the ListBox and clicking on an up or down arrow to move them in the desired direction. Pretty standard stuff.

As the application implements the MVVM pattern, the order of items is handled entirely in the ViewModel. Each item contains an `Order` property (an integer) which corresponds to its index in the sorted collection. Moving an item up one subtracts 1 from the item's `Order` and adds 1 to the `Order` of the item whose place it is taking. The inverse goes for moving an item down.

The problem that I encountered came about when trying to represent this order in the view. Binding an observable collection to a ListBox is easy. Getting the elements in the order specified by the user is another matter.

If you're like me and trying to get this to work, the first thing you'll do is try to apply a `SortDescription` to the ListBox. While the `SortDescription` will indeed sort the elements when the view is first loaded, when the user starts making changes to the `Order` values of the objects displayed in the ListBox, these changes will not be reflected in the view.

The solution I came across was buried in [this MSDN forums post](http://social.msdn.microsoft.com/Forums/vstudio/en-US/d7eda358-ca16-4164-8773-fd92527c7795/collectionviewsource-sort-not-reflecting-automatically-after-observablecollection-item-property?forum=wpf). The user named [demler](http://social.msdn.microsoft.com/profile/demler/?ws=usercard-mini) implemented a custom `CollectionViewSource` which handles the reordering for you. You can declare the custom `CollectionViewSource` in your XAML and bind it to an `ObservableCollection`. You can then bind your ListBox to the `CollectionViewSource` and as if by magic, the ordering is taken care of.

Here is the code for the `CollectionViewSource`. 

{% highlight csharp %}
public class AutoRefreshCollectionViewSource : CollectionViewSource
    {
        protected override void OnSourceChanged(object oldSource, object newSource)
        {
            if (oldSource != null)
                SubscribeSourceEvents(oldSource, true);
            if (newSource != null)
                SubscribeSourceEvents(newSource, false);

            base.OnSourceChanged(oldSource, newSource);
        }

        void Item_PropertyChanged(object sender, PropertyChangedEventArgs e)
        {
            var refresh = SortDescriptions.Any(x => x.PropertyName == e.PropertyName) ||
                GroupDescriptions.OfType<PropertyGroupDescription>().Any(x => x.PropertyName == e.PropertyName);

            if (refresh)
                View.Refresh();
        }


        void Source_CollectionChanged(object sender, NotifyCollectionChangedEventArgs e)
        {
            if (e.Action == NotifyCollectionChangedAction.Add)
                SubscribeItemsEvents(e.NewItems, false);
            else if (e.Action == NotifyCollectionChangedAction.Remove)
                SubscribeItemsEvents(e.OldItems, true);
            <
            // TODO: Support other actions if necessary.
        }


        void SubscribeItemEvents(object item, bool remove)
        {
            var notify = item as INotifyPropertyChanged;
            if (notify != null)
            {
                if (remove)
                    notify.PropertyChanged -= Item_PropertyChanged;
                else
                    notify.PropertyChanged += Item_PropertyChanged;
            }
        }

        void SubscribeItemsEvents(IEnumerable items, bool remove)
        {
            foreach (var item in items)
                SubscribeItemEvents(item, remove);
        }

        void SubscribeSourceEvents(object source, bool remove)
        {
            var notify = source as INotifyCollectionChanged;
            if (notify != null)
            {
                if (remove)
                    notify.CollectionChanged -= Source_CollectionChanged;
                else
                    notify.CollectionChanged += Source_CollectionChanged;
            }

            SubscribeItemsEvents((IEnumerable)source, remove);
        }
    }
{% endhighlight %}

You can then declare it in your XAML, binding it to the colleciton of your choice, as follows.


Once you've done that, simply point your ListBox to it like so.

That's all you need to do. The `AutoRefreshCollectionViewSource` acts as a middleman which silently ensures that everything is displayed in the 