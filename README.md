这项改进将使插件开发者能够更轻松地定制投掷物体的行为。

在当前的PocketMine插件API中，我们已经有了类似的功能，比如EntityShootBowEvent，但令人困惑的是，为什么还没有类似的特性用于ProjectileLaunchEvent呢？

**现有的方法**

目前，我们可以通过以下方式来改变投掷物体的行为：

```php
// 在 ProjectileLaunchEvent 中设置投掷物体的速度
$event->getEntity()->setMotion($motion->normalize()->multiply($newForce));
```

此外，还有一种“hack”方式，即通过重写内置的投掷物体类来覆盖getThrowForce()方法，但这种方法并不被支持，且会导致不必要的麻烦。

**问题**

然而，目前存在一些问题需要解决：

1. 如果在ProjectileLaunchEvent事件中使用setMotion()方法改变了投掷物体的速度，同时又修改了力量，那么预期的行为将会是什么样的？目前尚不清楚。
2. 在当前的API中，ProjectileLaunchEvent事件中的投掷物体在事件被调用之前就已经设置了它的速度为directionVector->multiply(force)。因此，如果我们在事件中使用setMotion()方法来改变速度，将会产生不确定的结果。

**可能的解决方案**

针对以上问题，我们提出了两种解决方案：

1. 如果在事件期间使用了setMotion()方法，我们可以选择忽略force参数，但这可能会导致意外的行为。
2. 另一种可能的解决方案是，规范化实体的运动，并始终将其乘以所需的力量。然而，这种方法将会导致向后不兼容性。

请注意，这些改进主要是为了使插件开发者能够更灵活地控制投掷物体的行为，使其能够更容易地实现例如投掷物体跳跃等高级功能。
