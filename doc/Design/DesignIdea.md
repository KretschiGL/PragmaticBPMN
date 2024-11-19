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
    private ModelingContext ctx = new ModelingContext(this);

    public TElement CreateNew<TElement>(string? id = null)
        where TElement : Element
    {
        var element = new TElement();
        element.Init(id ?? Guid.NewGuid().ToString(), ctx)
        return element;
    }

    public TElement Attach(TElement)
        where TElement : Element
    {
        // First, check if already attached
        element.AttachTo(ctx);
        return element;
    }

    internal void Register(Element element)
    {
        //... some code
    }

    internal void Unregister(Element element)
    {
        //... some code
    }
}

internal class ModelingContext(Model model) : IModelingContext
{
    internal void Register(Element element)
    {
        model.Register(element);
    }

    internal void Unregister(Element element)
    {
        model.Unregister(element);
    }
}

abstract class Element : IElement
{
    private IModelingContext ctx = default!;

    internal void Init(string id, IModelingContext ctx)
    {
        Id = CreateIdWith(id);
        AttachTo(ctx);
    }

    protected abstract ElementId CreateIdWith(string id);

    internal void AttachTo(IModelingContext ctx)
    {
        this.ctx?.Unregister(this);
        this.ctx = ctx;
        this.ctx.Register(this);
    }
}

```
