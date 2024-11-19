# Thoughts on Design

There needs to be a central `Model` that needs to know all the elements.
Elements should be linked to each other directly, but the model still needs to know them.

The main question is:

> How to create new elements, and ensure registration to the model?

## Factory Method

```CSharp

var model = new Model();
model.CreateNew<StartEvent>();
model.CreateNew<Task>(id: "asdf")
model.Attach(endEvent)

class Model
{
    private IModelingContext ctx = new ModelingContext(this);

    public TElement CreateNew<TElement>(string? id = null)
        where TElement : Element
    {
        var element = new TElement();
        element.Init(id ?? Guid.NewGuid(), ctx)
        return element;
    }

    public TElement Attach(TElement)
        where TElement : IElement
    {
        Register(element);
        return element;
    }

    internal void Register(IElement element)
    {
        //... some code
    }
}

internal class ModelingContext(Model model) : IModelingContext
{
    internal void Register(IElement element)
    {
        model.Register(element);
    }
}

abstract class Element : IElement
{
    private IModelingContext ctx = default!;

    internal void Init(string id, IModelingContext ctx)
    {
        this.ctx = ctx;
        Id = CreateIdWith(id);
        ctx.Register(this);
    }
}

```
