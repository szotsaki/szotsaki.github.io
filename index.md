---
---
# My first „Hello World!” processor!

This guide will show you how to create the new processor of yours, add some layers to it and set their properties.

*A bit of disclaimer: this guide will continuously evolve as developer documentations do. But hey, it exists! So it worth to come back from time to time or subscribe to its change-notification. The latest state of this text always reflects the HEAD version of branch „szotsaki”. Generally speaking, if something doesn't work for you, just merge and recompile everything.*

## So, the basics
You want to write a processor which does some calculations. You also want to show your work and later maybe interact with it on the GUI. For all of these you are needed to deal with three classes:
1. `Processor`: of course this is the most important one. All the essence of your work goes mostly here.
2. `Layer`: if you want some show-off, you'll also need this. Or these. You have the possibility to attach more layers to a processor.
3. `ProcessorSlot`: last but not least, a structure is needed which encompasses these aforementioned classes together. You won't need to care about this much though, its defaults are just perfect.

### Processors
First, start with the `Processor`. It has a *name* and arbitrary number of custom *properties*. The processor-related files are stored with the model, in the <tt>MarbeCommon/Processors</tt> directory. To create your first processor, let's call it `FirstProcessor`, you have to subclass from the abstract `Processor` class like this:

```c++
class FirstProcessor : public Processor
{
    Q_OBJECT
public:
    FirstProcessor(QObject *parent = nullptr);
    ~FirstProcessor() = default;
}
```

The `Q_OBJECT` macro is necessary because of some boring administration reasons. The `parent` pointer is necessary because [Qt](http://www.qt.io/) maintains a hierarchy of the declared objects for easier deletion among others. After that you see the constructor and destructor. Here is, how to define this class:

```c++
FirstProcessor::FirstProcessor(QObject *parent)
    : Processor("My first Processor", parent)
{
}
```

We pass the name of our processor (which will be shown on the GUI) to the parent constructor.

### Layers
Now, head towards to the `Layers`.

The resemblance is uncanny with the processors. You will just have to create a new `Layer` class of yours (call it, for the sake of simplicity, `FirstLayer`), make it a descendant of the `Layer` abstract class and pass your *layer's name* to the parent constructor. The layer-related files lie with other GUI files, in the <tt>GrainAutLine/Layers</tt> directory. A very basic implementation would look like this:

```c++
class FirstLayer : public Layer
{
    Q_OBJECT
public:
    explicit FirstLayer(QObject *parent = nullptr);
    ~FirstLayer() = default;
}
```

```c++
FirstLayer::FirstLayer(QObject *parent)
    : Layer("My first layer", parent)
{
}
```

### The ProcessorSlot
Let's finish with the encapsulating class, the `ProcessorSlot`.

First, you need to shortly name your work you did so far. The name of the compilation will appear only in code but it should be expressive for other developers. Register this name in the `ProcessorSlot::Type` enumeration. Now you can create a new specific instance of `ProcessorSlot`: please, usher yourself to the hall of the `ProcessorSlot` constructor and supplement the switch-case structure with the name of your choice.

The `Processor_` variable will hold your custom processor. Create a new instance of it:
```c++
Processor_ = std::make_shared<FirstProcessor>(this);
```
The `Layers` vector holds all the layers you want to use later. Previously we created only one, `FirstLayer`, but feel free to either add it multiple times or create many different layers later and add them here:
```c++
Layers.push_back(std::make_shared<FirstLayer>(this));
```

### The almighty encapsulator: the SlotManager
Maybe there's a slight chance you've been wondering if there is a encapsulation class for `Layers` and `Processors` (this is the `ProcessorSlot`), is there a similar container for many `ProcessorSlots`? Yes, there is. It is called `SlotManager` which, not surprisingly, manages the life cycles of `ProcessorSlots`.

When you are ready with your modifications so far, you are needed to register your `ProcessorSlot` in `SlotManager`. This is quite easy, just add your class to the following container in its constructor like this:
```c++
ProcessorSlots.push_back(std::make_shared<ProcessorSlot>(ProcessorSlot::Type::MyFirstProcessorSlot, this));
```

Make sure that you replace `MyFirstProcessorSlot` with the name you used previously in the enumeration.

### Finishing

You created a new  `Processor` and a new `Layer`. Then you added them to the their encapsulation class, `ProcessorSlot`. After that you registered this processor slot at their manager, `SlotManager`.

Now, we are ready. Oh, just one thing: make sure that your processor computes and layer draws. How easy to say that, isn't it? Well, it will surely be a joyful journey. For the beginning, define and override the following functions:
- for the computation itself:
```c++
virtual bool FirstProcessor::Step(ProcessingStateDescriptor& psd) override;
```
- and for the drawing:
```c++
virtual void FirstLayer::Render(const ProcessingStateDescriptor& psd) override;
```

Have a nice time coding!
