# Introduction To DataClasses

![image1](edgesd.png)

## Introduction

Data classes in Python provide a powerful and concise way to define classes that are primarily used to store data. They reduce boilerplate code, improve readability, and integrate well with Python's type hinting. By understanding and utilizing data classes, you can write cleaner and more maintainable code for your data-centric classes.

Dataclasses are a feature in Python that provides a decorator and functions for automatically adding special methods to user-defined classes. They were introduced in Python 3.7 via PEP 557 and simplify the process of creating classes that are primarily used to store data.

### What is a dataclass?

A dataclass is a class decorated with @dataclass from the dataclasses module. This decorator automatically generates special methods based on the class attributes, such as the initializer __init__, string representation __repr__, and equality __eq__.

Dataclasses require type annotations for all fields. This helps to clearly define what type each field should be:

Example 1: This example demonstrates a simple Data Class for handling resolution settings in an application without default values.

```ruby
from dataclasses import dataclass

@dataclass
class ResolutionAppConfig():
    """Resolution dataclass."""

    height: int
    width: int
```

The ResolutionAppConfig class, stores the height and width of a resolution.

Example 2: In this example, we create a Data Class for video configuration settings. It includes default values and optional nested Data Classes.

```ruby
from dataclasses import dataclass, field
from typing import Optional

@dataclass
class VideoAppConfig:
    """Video config dataclass."""
    name: str
    resolution: Optional[ResolutionAppConfig] = None
    max_fps: int = 30
    preview: bool = False
    focus_control: bool = False
```

Here, VideoAppConfig includes attributes such as name, resolution, max_fps, preview, and focus_control. The resolution attribute is an optional instance of ResolutionAppConfig, demonstrating how Data Classes can be nested.

Note: Without using dataclasses, you would need to manually define dunder (double underscore) methods such as __init__, __repr__, and __eq__ to achieve the same level of functionality and readability that dataclasses provide out of the box. We provide an example of such as you read along.

Example 3: Here is an example of a TrainingConfig data class that include a wide range of hyperparameters and configurations for training a model.

```ruby
from dataclasses import dataclass
from typing import Optional

@dataclass
class TrainingConfig:
    """Training configuration dataclass."""
    epochs: int
    learning_rate: float
    batch_size: int
    optimizer: str
    validation_split: float = 0.2
    shuffle: bool = True
    early_stopping: bool = False
    checkpoint_path: Optional[str] = None

@dataclass
class TrainingModel:
    """Training model dataclass."""
    config: TrainingConfig

# Usage
config = TrainingConfig(
    epochs=10,
    learning_rate=0.01,
    batch_size=32,
    optimizer='adam',
    validation_split=0.2,
    shuffle=True,
    early_stopping=True,
    checkpoint_path='./checkpoints/model.ckpt'
)

model = TrainingModel(config=config)

print(model)
```
Output:


```ruby
TrainingModel(config=TrainingConfig(epochs=10, learning_rate=0.01, batch_size=32, optimizer='adam', validation_split=0.2, shuffle=True, early_stopping=True, checkpoint_path='./checkpoints/model.ckpt'))
```
It uses the automatically generated __repr__ method from the dataclasses module to produce a readable string representation of the object.


### Without Using Dataclasses

When creating a class in Python without using dataclasses, you manually manage the class attributes and behaviors. This involves writing methods to initialize the attributes, provide a string representation, and compare instances.

```ruby
from typing import Optional, List

class TrainingConfig:
    """Training configuration class."""
    def __init__(self, 
                 epochs: int, 
                 learning_rate: float, 
                 batch_size: int, 
                 optimizer: str, 
                 validation_split: float = 0.2, 
                 shuffle: bool = True, 
                 early_stopping: bool = False, 
                 checkpoint_path: Optional[str] = None):
        self.epochs = epochs
        self.learning_rate = learning_rate
        self.batch_size = batch_size
        self.optimizer = optimizer
        self.validation_split = validation_split
        self.shuffle = shuffle
        self.early_stopping = early_stopping
        self.checkpoint_path = checkpoint_path

    def __repr__(self):
        return (f"TrainingConfig(epochs={self.epochs}, learning_rate={self.learning_rate}, "
                f"batch_size={self.batch_size}, optimizer='{self.optimizer}',"
                f"validation_split={self.validation_split}, shuffle={self.shuffle}, "
                f"early_stopping={self.early_stopping}, checkpoint_path='{self.checkpoint_path}')")

    def __eq__(self, other):
        if not isinstance(other, TrainingConfig):
            return NotImplemented
        return (self.epochs == other.epochs and self.learning_rate == other.learning_rate
                self.batch_size == other.batch_size and self.optimizer == other.optimizer
                self.validation_split == other.validation_split and self.shuffle == other.shuffle
                self.early_stopping == other.early_stopping and self.checkpoint_path == other.checkpoint_path)


class TrainingModel:
    """Training model class."""
    def __init__(self, config: TrainingConfig):
        self.config = config

    def __repr__(self):
        return f"TrainingModel(config={self.config})"

    def __eq__(self, other):
        if not isinstance(other, TrainingModel):
            return NotImplemented
        return self.config == other.config

#creating an instance
config = TrainingConfig(
    epochs=10,
    learning_rate=0.01,
    batch_size=32,
    optimizer='adam',
    validation_split=0.2,
    shuffle=True,
    early_stopping=True,
    checkpoint_path='./checkpoints/model.ckpt'
)

model = TrainingModel(config=config)

print(model)
```
Output:

```ruby
TrainingModel(config=TrainingConfig(epochs=10, learning_rate=0.01, batch_size=32, optimizer='adam', validation_split=0.2, shuffle=True, early_stopping=True, checkpoint_path='./checkpoints/model.ckpt'))
```
When you print the model instance, the custom __repr__ methods in both TrainingConfig and TrainingModel classes will produce a readable string representation of the object. The output will look the same as the one produced by dataclasses, however it makes the code verbose.


### What would happen when we don’t define the dunder methods in a class?

If you do not define the __init__ method, the class will not be able to initialize instance variables based on any parameters passed during object creation. Instead, it will rely on a default initializer that does nothing.

If you do not define the __repr__ method, the default implementation provided by Python will return a string that includes the object's class name and its memory address. This is typically not very informative for understanding the state of the object.

If you do not define the __eq__ method, the default implementation will compare objects by their memory addresses, not by their content. This means two different instances of the same class with the same attribute values will not be considered equal.

```ruby
class TrainingConfig:
    pass

# Creating an instance of the class
config = TrainingConfig()

# Default __repr__ method output
print(config)

# Attempting to check equality (will compare memory addresses)
another_config = TrainingConfig()
print(config == another_config)
```
Output:

```ruby
<__main__.TrainingConfig object at 0x7f9c886fb1c0>
False
```

The output <__main__.TrainingConfig object at 0x7f9c886fb1c0> indicates that the __repr__ method is not defined in the TrainingConfig class, leading to a default representation that includes the class name and memory address. Additionally, the output False signifies that two instances of the class are not considered equal due to the lack of a custom __eq__ method.

```ruby
class TrainingConfig:
    pass

# Creating instances
config1 = TrainingConfig()
config2 = TrainingConfig()

# Attempting to access attributes
try:
    print(config1.epochs)
except AttributeError as e:
    print(e)  # 'TrainingConfig' object has no attribute 'epochs'

# Setting attributes manually
config1.epochs = 10
config1.learning_rate = 0.01

config2.epochs = 10
config2.learning_rate = 0.01

# Default __repr__ method output
print(config1)
print(config2)

# Checking equality
print(config1 == config2)
```
Ouptut:

```ruby
'TrainingConfig' object has no attribute 'epochs'
<__main__.TrainingConfig object at 0x7f9c886fb1c0>
<__main__.TrainingConfig object at 0x7f9c886fb220>
False
```

## Suggested solution

Here’s how to manually manage initialization, representation, and comparison:

Initialization: Manually assign attributes after creating the object.

String Representation: Create a method to return a string representation of the object.

Comparison: Create a method to compare two objects based on their attributes.

Manually managing initialization, representation, and comparison without dunder methods can make your code less maintainable and modular. Dunder methods in Python are designed to handle these tasks efficiently and succinctly, providing built-in ways to manage object behaviour that follow standard conventions, however data classes does it cleaner.

Conclusion

Using dataclasses: This approach is concise and automatically provides methods like __init__, __repr__, and __eq__, which makes the code cleaner, quality code and easier to maintain.

Without dataclasses: You need to manually define the __init__, __repr__, and __eq__ methods, which can be more verbose and prone to human error, but it gives you full control over the class behaviour.

For more information, you can check this video below:

Tech With Tim

indently

Additionally, check the doc for more features:

dataclass_documentation
