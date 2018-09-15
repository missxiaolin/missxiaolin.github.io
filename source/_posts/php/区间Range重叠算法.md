---
title: 区间Range重叠算法
date: 2018-09-15 16:30:46
categories: ["PHP"]
tags: 'php'
---

还记得上代数课求两个连续集合是否有交集的场景么？

例如：

- 连续集合A (2, 8)
- 连续集合B (6, +∞)

~~~
                    _______________________
      ______________|_________
      |             |        |
------o-------------o--------o------------>
      2             6        8
~~~

我们学习时，可以通过画数轴，划范围，看两个区间是否重合区域的图像法求解。那如何通过程序，让计算机能判断这样的结果呢？

## 构思

数轴其实是由无数的点组成。每个连续集合是由一个起始点(特例-∞)和一个结束点组成(特例+∞)。因此要完成这个工作，底层至少需要两个类:

### Point

- 属性 value，代表数值。需要考虑特例-∞和特例+∞
- 属性 includeSelf，代表是否包含自身，其实就是实心点还是虚心点的区别

### Range

- 属性 start，代表起始点
- 属性 end，代表结束点
- 功能 hasIntersection(Range $range)，判断当前对象与传入的对象是否含有交集

## 具体实现代码

### Point 类

~~~
/**
 * 数据点对象
 *
 * Class Point
 * @package common\models\range
 */
class Point
{
    /**
     * 正无穷
     */
    const POSITIVE_INFINITY = "+";

    /**
     * 负无穷
     */
    const NEGATIVE_INFINITY = "-";

    /**
     * @var float|string 数值
     */
    public $value;

    /**
     * @var bool 是否包含自己
     */
    public $includeSelf = true;

    /**
     * 私有化构造方法，不让外面直接new
     *
     * Point constructor.
     */
    private function __construct() {}

    /**
     * 构建有限集合点(不包含负无穷和正无穷的点)
     *
     * @param float $value
     * @param bool  $includeSelf
     * @return Point
     */
    public static function builderPoint(float $value, bool $includeSelf) {
        $point = new Point();
        $point->value = $value;
        $point->includeSelf = $includeSelf;
        return $point;
    }

    /**
     * 构建正无穷的点
     *
     * @return Point
     */
    public static function builderPositiveInfinityPoint()
    {
        $point = new Point();
        $point->value = Point::POSITIVE_INFINITY;
        $point->includeSelf = false;
        return $point;
    }

    /**
     * 构建负无穷的点
     *
     * @return Point
     */
    public static function builderNegativeInfinityPoint()
    {
        $point = new Point();
        $point->value = Point::NEGATIVE_INFINITY;
        $point->includeSelf = false;
        return $point;
    }

    /**
     * 判断是否是负无穷的点
     *
     * @return bool
     */
    public function isNegativeInfinityPoint() {
        return $this->value === self::NEGATIVE_INFINITY;
    }

    /**
     * 判断是否是正无穷的点
     *
     * @return bool
     */
    public function isPositiveInfinityPoint() {
        return $this->value === self::POSITIVE_INFINITY;
    }
}
~~~

### Range 类

~~~
/**
 * 数据区间
 *
 * Class Range
 * @package common\models\range
 */
class Range
{
    /**
     * @var Point 起始点
     */
    public $start;

    /**
     * @var Point 结束点
     */
    public $end;

    /**
     * 私有化构造方法，不让外面直接new
     *
     * Range constructor.
     */
    private function __construct() {}

    /**
     * 构造Range对象
     *
     * @param Point $start 起始点
     * @param Point $end 结束点
     * @return Range
     * @throws RangeInvalidException
     */
    public static function builderRange(Point $start, Point $end)
    {
        $range = new Range();
        $range->start = $start;
        $range->end = $end;

        if ($range->isValidRange()) {
            return $range;
        } else {
            throw new RangeInvalidException("{$range}不满足数学区间定义");
        }
    }


    /**
     * 判断当前集合是否合法
     *
     * @return bool true-合法; false-非法
     */
    public function isValidRange() {
        if ($this->start->isNegativeInfinityPoint() && $this->end->isNegativeInfinityPoint()) {
            return false;
        } else if ($this->start->isPositiveInfinityPoint() && $this->end->isPositiveInfinityPoint()) {
            return false;
        } else if ($this->start->isPositiveInfinityPoint()) {
            return false;
        } else if ($this->end->isNegativeInfinityPoint()) {
            return false;
        } else if (!$this->start->isNegativeInfinityPoint() && !$this->end->isPositiveInfinityPoint()) {
            if ($this->start->value > $this->end->value) {
                // 起始点大于结束点，不合法
                return false;
            } else if ($this->start->value == $this->end->value) {
                if ($this->start->includeSelf && $this->end->includeSelf) {
                    // 这种说明区间是一个实心点，合法
                    return true;
                } else {
                    return false;

                }
            }
        }
        return true;
    }

    function __toString()
    {
        $result = "";

        // 起始点
        if ($this->start->isNegativeInfinityPoint()) {
            $result .= "(-∞, ";
        } else If ($this->start->isPositiveInfinityPoint()) {
            $result .= "(+∞, ";
        } else {
            if ($this->start->includeSelf) {
                $result .= "[" . $this->start->value . ", ";
            } else {
                $result .= "(" . $this->start->value . ", ";
            }
        }

        // 结束点
        if ($this->end->isNegativeInfinityPoint()) {
            $result .= "-∞)";
        } else If ($this->end->isPositiveInfinityPoint()) {
            $result .= "+∞)";
        } else {
            if ($this->end->includeSelf) {
                $result .= $this->end->value . "]";
            } else {
                $result .= $this->end->value . ")";
            }
        }

        return $result;
    }

    /**
     * 是否包含交集部分
     *
     * @param Range $other 另一个被比较的交集对象
     * @return bool true-含有交集; false-不含交集
     */
    public function hasIntersection(Range $other)
    {
        if ($this->start->isNegativeInfinityPoint() && $this->end->isPositiveInfinityPoint()) {
            // 因为当前对象是负无穷到正无穷，所以不论other什么取值，总是会有交集的
            return true;
        } else if ($this->start->isNegativeInfinityPoint()) {
            // 当前对象左侧到负无穷，右侧有限。这种情况，只有other在右侧才有可能没有交集
            if ($other->start->isNegativeInfinityPoint()) {
                return true;
            } else if ($other->start->value == $this->end->value) {
                if ($other->start->includeSelf && $this->end->includeSelf) {
                    return true;
                } else {
                    return false;
                }
            } else if ($other->start->value > $this->end->value) {
                return false;
            } else {
                return true;
            }
        } else if ($this->end->isPositiveInfinityPoint()) {
            // 当前对象右侧到正无穷，左侧有限。这种情况，只有other在左侧，才有可能没有交集
            if ($other->end->isPositiveInfinityPoint()) {
                return true;
            } else if ($other->end->value == $this->start->value) {
                if ($other->end->includeSelf && $this->start->includeSelf) {
                    return true;
                } else {
                    return false;
                }
            } else if ($other->end->value < $this->start->value) {
                return false;
            } else {
                return true;
            }
        } else {
            // 当前对象是一个有限区间
            if ($other->start->isNegativeInfinityPoint() && $other->end->isPositiveInfinityPoint()) {
                // 因为other对象是负无穷到正无穷，所以不论this什么取值，总是会有交集的
                return true;
            } else if ($other->start->isNegativeInfinityPoint()) {
                // other对象左侧到负无穷，右侧有限。
                if ($other->end->value == $this->start->value) {
                    // 判断实心点还是虚心点
                    if ($other->end->includeSelf && $this->start->includeSelf) {
                        return true;
                    } else {
                        return false;
                    }
                } else if($other->end->value < $this->start->value) {
                    return false;
                } else {
                    return true;
                }
            } else if ($other->end->isPositiveInfinityPoint()) {
                // other对象右侧正无穷，左侧有限
                if ($other->start->value == $this->end->value) {
                    // 判断实心点还是虚心点
                    if ($other->start->includeSelf && $this->end->includeSelf) {
                        return true;
                    } else {
                        return false;
                    }
                } else if ($other->start->value > $this->end->value) {
                    return false;
                } else {
                    return true;
                }
            } else {
                // other对象是一个有限区间

                // other对象的区间在this对象区间的左侧，才有可能没有交集
                if ($other->end->value == $this->start->value) {
                    if ($other->end->includeSelf && $this->start->includeSelf) {
                        return true;
                    } else {
                        return false;
                    }
                } else if ($other->end->value < $this->start->value) {
                    return false;
                }

                // other对象的区间在this对象区间的右侧，才有可能没有交集
                if ($other->start->value == $this->end->value) {
                    if ($other->start->includeSelf && $this->end->includeSelf) {
                        return true;
                    } else {
                        return false;
                    }
                } elseif ($other->start->value > $this->end->value) {
                    return false;
                }

                // 否则就有交集
                return true;
            }
        }
    }

    /**
     * 判断给定的点，是否在区间内
     *
     * @param Point $point
     * @return bool true-在区间内; false-不在区间内
     */
    public function isContainPoint(Point $point) {
        if ($this->start->isNegativeInfinityPoint() && $this->end->isPositiveInfinityPoint()) {
            // 因为当前区间是负无穷到正无穷，所以不论 $point 什么取值，总是会在区间内
            return true;
        } else if ($this->start->isNegativeInfinityPoint()) {
            // 当前区间的左侧负无穷，右侧有极限。这样 $point 需要在右侧才有可能不在区间内
            if ($point->value == $this->end->value) {
                if ($point->includeSelf && $this->end->includeSelf) {
                    return true;
                } else {
                    return false;
                }
            } else if ($point->value > $this->end->value) {
                return false;
            } else {
                return true;
            }
        } else if ($this->end->isPositiveInfinityPoint()) {
            // 当前区间的右侧正无穷，左侧有极限。这样 $point 需要在左侧才有可能不在区间内
            if ($point->value == $this->start->value) {
                if ($point->includeSelf && $this->start->includeSelf) {
                    return true;
                } else {
                    return false;
                }
            } else if ($point->value < $this->start->value) {
                return false;
            } else {
                return true;
            }
        } else {
            // 当前区间是一个有限区间
            if ($point->value == $this->start->value) {
                if ($point->includeSelf && $this->start->includeSelf) {
                    return true;
                } else {
                    return false;
                }
            } else if ($point->value == $this->end->value) {
                if ($point->includeSelf && $this->end->includeSelf) {
                    return true;
                } else {
                    return false;
                }
            } else if ($point->value > $this->start->value && $point->value < $this->end->value) {
                return true;
            } else {
                return false;
            }
        }
    }
}
~~~

### RangeInvalidException 类

~~~
/**
 * 区间非法异常
 *
 * Class RangeInvalidException
 * @package common\models\range
 */
class RangeInvalidException extends \Exception
{
}
~~~

## 测试代码

### 测试含有重叠场景

~~~
$rangeA = Range::builderRange(Point::builderPoint(2, false), Point::builderPoint(8, false));
dump("rangeA:" . $rangeA);

$rangeB = Range::builderRange(Point::builderPoint(6, false), Point::builderPositiveInfinityPoint());
dump("rangeB:" . $rangeB);

dd("rangeA hasIntersection " . ($rangeA->hasIntersection($rangeB) ? "Yes" : "No"));
~~~

运行结果:

~~~
"rangeA:(2, 8)"
"rangeB:(6, +∞)"
"rangeA hasIntersection Yes"
~~~

### 测试没有重叠的场景

~~~
$rangeA = Range::builderRange(Point::builderPoint(2, false), Point::builderPoint(8, false));
dump("rangeA:" . $rangeA);

$rangeB = Range::builderRange(Point::builderPoint(10, false), Point::builderPositiveInfinityPoint());
dump("rangeB:" . $rangeB);

dd("rangeA hasIntersection " . ($rangeA->hasIntersection($rangeB) ? "Yes" : "No"));
~~~

运行结果:

~~~
"rangeA:(2, 8)"
"rangeB:(10, +∞)"
"rangeA hasIntersection No"
~~~



