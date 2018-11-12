#德州扑克牌型算法

###在这样的前提下：

花色 | 数值 | 牌|
----|----|----|
黑桃 |0 -12 |2-A|   
红桃 |13-25|2-A|
草花 |26-38|2-A| 
方块 |39-51|2-A| 

牌型|数值
---|---
[皇家同花顺](#皇家同花顺) | 10
[同花顺](#同花顺)|9
[四条(金刚)](#四条)|8
[葫芦](#葫芦)|7
[同花](#同花)|6
[顺子](#顺子)|5
[三条](#三条)|4
[两对](#两对)|3
[对子](#对子)|2
高牌|1

#### 牌型判断
```
	牌型 A
    if ([self resultRoyalFlush:cards]) {
		A =	皇家同花顺
    } else if ([self resultStraightFlush:cards]) {
		A =	同花顺
    } else if ([self resultFourOfaKind:cards]) {
		A =	四条
    } else if ([self resultFullHouse:cards]) {
		A =	葫芦
    } else if ([self resultFlush:cards]) {
		A =	同花
    } else if ([self resultStraight:cards]) {
		A =	顺子
    } else if ([self resultThreeOfaKind:cards]) {
		A =	三条
    } else if ([self resultTwoPairs:cards]) {
		A =	两对
    } else if ([self resultOnePair:cards]) {
		A =	对子
    } else {
		A =	高牌
    }
    
```
#### <a id="对子"></a>对子
**5**张牌变为**4**张牌 即为对子

```
	- (BOOL)resultOnePair :(NSArray *)cards
{
    BOOL result = false;
    NSMutableSet *deformationCards = [NSMutableSet setWithCapacity:0];
    for (NSInteger i = 0; i < cards.count; i++) {
        [deformationCards addObject:[NSString stringWithFormat:@"%ld",[cards[i] integerValue]%13]];
    }
    if (deformationCards.count == 4) {
        result = true;
    }
    return result;
}

```
#### <a id="两对"></a>两对
**5**张牌变为**3**张牌 且有一个牌有**2**张

```
- (BOOL)resultTwoPairs:(NSArray *)cards
{
    BOOL result = false;
    NSMutableSet *deformationCards = [NSMutableSet setWithCapacity:0];
    for (NSInteger i = 0; i < cards.count; i++) {
        [deformationCards addObject:[NSString stringWithFormat:@"%ld",[cards[i] integerValue]%13]];
    }
    if (deformationCards.count == 3) {
        int max = [[deformationCards valueForKeyPath:@"@max.intValue"] intValue];
        int min = [[deformationCards valueForKeyPath:@"@min.intValue"] intValue];
        int otherValue = -1;
        for (NSString *numberStr in deformationCards) {
            if ([numberStr intValue] != max && [numberStr intValue] != min) {
                otherValue = [numberStr intValue];
                break;
            }
        }
        NSInteger totalMaxCount = 0;
        NSInteger totalMinCount = 0;
        NSInteger totalOtherValueCount = 0;
        for (NSString *numberStr in cards) {
            if ([numberStr intValue] %13 == max) {
                totalMaxCount ++;
            } else if ([numberStr intValue] %13 == min) {
                totalMinCount ++;
            } else if ([numberStr intValue] %13 == otherValue) {
                totalOtherValueCount ++;
            }
        }
        if (totalOtherValueCount == 2 || totalMinCount == 2 || totalOtherValueCount == 2) {
            result = true;
        }
    }
    return result;
}
```
#### <a id="三条"></a>三条
**5**张牌变为**3**张牌 且有一个牌为**3**个

```
- (BOOL)resultThreeOfaKind:(NSArray *)cards
{
    BOOL result = false;
    NSMutableSet *deformationCards = [NSMutableSet setWithCapacity:0];
    for (NSInteger i = 0; i < cards.count; i++) {
        [deformationCards addObject:[NSString stringWithFormat:@"%ld",[cards[i] integerValue]%13]];
    }
    if (deformationCards.count == 3) {
        int max = [[deformationCards valueForKeyPath:@"@max.intValue"] intValue];
        int min = [[deformationCards valueForKeyPath:@"@min.intValue"] intValue];
        int otherValue = -1;
        for (NSString *numberStr in deformationCards) {
            if ([numberStr intValue] != max && [numberStr intValue] != min) {
                otherValue = [numberStr intValue];
                break;
            }
        }
        NSInteger totalMaxCount = 0;
        NSInteger totalMinCount = 0;
        NSInteger totalOtherValueCount = 0;
        for (NSString *numberStr in cards) {
            if ([numberStr intValue] %13 == max) {
                totalMaxCount ++;
            } else if ([numberStr intValue] %13 == min) {
                totalMinCount ++;
            } else if ([numberStr intValue] %13 == otherValue) {
                totalOtherValueCount ++;
            }
        }
        if (totalOtherValueCount == 3 || totalMinCount == 3 || totalOtherValueCount == 3) {
            result = true;
        }
    }
    return result;
}
```
#### <a id="顺子"></a>顺子
**5**张 且最大值**-**最小值=**4** 或 (A 2 3 4 5)数值相加为**18** 

```
- (BOOL)resultStraight:(NSArray *)cards
{
    BOOL result = false;
    NSMutableSet *deformationCards = [NSMutableSet setWithCapacity:0];
    for (NSInteger i = 0; i < cards.count; i++) {
        [deformationCards addObject:[NSString stringWithFormat:@"%ld",[cards[i] integerValue]%13]];
    }
    if (deformationCards.count == 5) {
        int max = [[deformationCards valueForKeyPath:@"@max.intValue"] intValue];
        int min = [[deformationCards valueForKeyPath:@"@min.intValue"] intValue];
        if (max - min == 4) {
            result = true;
        } else {
            if (max == 12) {
                //A
                NSInteger total = 0;
                for (NSString *numberStr in deformationCards) {
                    total += [numberStr integerValue];
                }
                if (total == 18) {
                    result = true;
                }
            }
        }
    }
    return result;
}
```
#### <a id="同花"></a>同花

**5**张牌 且**最大值**和**最小值**都处于**同一花色值**内

```
- (BOOL)resultFlush:(NSArray *)cards
{
    BOOL result = false;
    NSMutableSet *deformationCards = [NSMutableSet setWithCapacity:0];
    for (NSInteger i = 0; i < cards.count; i++) {
        [deformationCards addObject:[NSString stringWithFormat:@"%ld",[cards[i] integerValue]]];
    }
    if (deformationCards.count == 5) {
        int max = [[deformationCards valueForKeyPath:@"@max.intValue"] intValue];
        int min = [[deformationCards valueForKeyPath:@"@min.intValue"] intValue];
        if (max - min <= 12) {
            if ((max >= 0 && max <= 12) && (min >= 0 && min <= 12)) {
                result = true;
            } else if ((max >=13 && max <= 25) && (min >= 13 && min <= 25)) {
                result = true;
            } else if ((max >= 26 && max <= 38) && (min >= 26 && min <= 38)) {
                result = true;
            } else if ((max >= 39 && max <= 51) && (min >= 39 && min <= 51)) {
                result = true;
            }
        }
    }
    return result;
}
```
#### <a id="葫芦"></a>葫芦

**5**张变**2**张 且有一个牌有**3**个

```
- (BOOL)resultFullHouse:(NSArray *)cards
{
    BOOL result = false;
    NSMutableSet *deformationCards = [NSMutableSet setWithCapacity:0];
    for (NSInteger i = 0; i < cards.count; i++) {
        [deformationCards addObject:[NSString stringWithFormat:@"%ld",[cards[i] integerValue]%13]];
    }
    if (deformationCards.count == 2) {
        int max = [[deformationCards valueForKeyPath:@"@max.intValue"] intValue];
        int min = [[deformationCards valueForKeyPath:@"@min.intValue"] intValue];
        NSInteger totalMaxCount = 0;
        NSInteger totalMinCount = 0;
        for (NSString *numberStr in cards) {
            if ([numberStr intValue]%13 == max) {
                totalMaxCount ++;
            } else if ([numberStr intValue]%13 == min) {
                totalMinCount ++;
            }
        }
        if (totalMinCount == 3 || totalMaxCount == 3) {
            result = true;
        }
    }
    return result;
}
```
#### <a id="四条"></a>四条
**5**张变**2**张 且有一个牌有**4**个

```
- (BOOL)resultFourOfaKind:(NSArray *)cards
{
    BOOL result = false;
    NSMutableSet *deformationCards = [NSMutableSet setWithCapacity:0];
    for (NSInteger i = 0; i < cards.count; i++) {
        [deformationCards addObject:[NSString stringWithFormat:@"%ld",[cards[i] integerValue]%13]];
    }
    if (deformationCards.count == 2) {
        int max = [[deformationCards valueForKeyPath:@"@max.intValue"] intValue];
        int min = [[deformationCards valueForKeyPath:@"@min.intValue"] intValue];
        NSInteger totalMaxCount = 0;
        NSInteger totalMinCount = 0;
        for (NSString *numberStr in cards) {
            if ([numberStr intValue]%13 == max) {
                totalMaxCount ++;
            } else if ([numberStr intValue]%13 == min) {
                totalMinCount ++;
            }
        }
        if (totalMinCount == 4 || totalMaxCount == 4) {
            result = true;
        }
    }
    return result;
}
```
#### <a id="同花顺"></a>同花顺

是**同花**且是**顺子**

```
- (BOOL)resultStraightFlush:(NSArray *)cards
{
    BOOL result = false;
    if ([self resultFlush:cards] && [self resultStraight:cards]) {
        result = true;
    }
    return result;
}
```
#### <a id="皇家同花顺"></a>皇家同花顺

是**同花顺**且含**A**

```
- (BOOL)resultRoyalFlush:(NSArray *)cards
{
    BOOL result = false;
    int max = [[cards valueForKeyPath:@"@max.intValue"] intValue];
    if ([self resultFlush:cards] && [self resultStraight:cards] && max == 12) {
        result = true;
    }
    return result;
}
```




