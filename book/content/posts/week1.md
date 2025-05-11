---
title: "Week1"
date: 2025-05-11T11:59:41+08:00
# bookComments: false
# bookSearchExclude: false
--- 
 
## 1. Two Sum (两数之和)

### 1.1 理解题目

* **题目描述**：给定一个整数数组 `nums` 和一个整数目标值 `target`，请你在该数组中找出和为目标值 `target` 的那两个整数，并返回它们的数组下标。
* **假设**：你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。你可以按任意顺序返回答案。
* **主要考察点**：数组遍历、哈希表的使用。
* **目的**：这道题旨在考察你是否能够有效地在数据集合中查找满足特定条件的元素对。它是哈希表应用的入门级经典题目。

### 1.2 从思路引出最优解决方案 (Go语言实现)

#### 思路一：暴力枚举 (Brute Force)

最直观的想法是遍历数组中的每一个元素 `x`，然后再遍历数组中 `x` 之后的每一个元素 `y`，判断 `x + y` 是否等于 `target`。

* **步骤**：
    1.  使用第一层循环遍历数组，假设当前元素是 `nums[i]`。
    2.  使用第二层循环遍历数组中 `i` 之后的元素，假设当前元素是 `nums[j]`。
    3.  如果 `nums[i] + nums[j] == target`，那么 `[i, j]` 就是我们要找的下标。

* **Go语言实现**：
    ```go
    package main

    import "fmt"

    func twoSum_bruteForce(nums []int, target int) []int {
        n := len(nums)
        for i := 0; i < n; i++ {
            for j := i + 1; j < n; j++ {
                if nums[i]+nums[j] == target {
                    return []int{i, j}
                }
            }
        }
        return nil // 题目保证有解，这里理论上不会执行
    }

    func main() {
        nums := []int{2, 7, 11, 15}
        target := 9
        fmt.Println(twoSum_bruteForce(nums, target)) // Output: [0, 1]

        nums2 := []int{3, 2, 4}
        target2 := 6
        fmt.Println(twoSum_bruteForce(nums2, target2)) // Output: [1, 2]
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N^2)$，其中 $N$ 是数组的长度。因为我们需要两层嵌套循环。
    * 空间复杂度：$O(1)$，我们只使用了常数级别的额外空间。

* **缺点**：当数组规模较大时，平方级别的时间复杂度会导致执行效率低下。

#### 思路二：哈希表优化 (Hash Table Optimization)

暴力枚举的瓶颈在于查找另一个数的过程。如果我们能快速知道 `target - nums[i]` 这个数是否存在于数组中，并且知道它的下标，问题就能高效解决。哈希表（在 Go 中是 `map`）提供了平均 $O(1)$ 时间复杂度的查找能力。

* **步骤**：
    1.  创建一个哈希表 `seen`，用于存储遍历过的数字及其下标，即 `value -> index`。
    2.  遍历数组 `nums`，对于每个数字 `num` (设其下标为 `i`)：
        a.  计算需要的“补充数” `complement = target - num`。
        b.  在哈希表 `seen` 中查找 `complement` 是否存在。
        c.  如果 `complement` 存在于 `seen` 中，并且其存储的下标 `j` 不等于当前下标 `i`（题目要求同一个元素不能重复使用，但由于我们是先查找后存入，如果找到了，一定是之前存入的另一个元素），那么我们就找到了答案 `[seen[complement], i]`。
        d.  如果 `complement` 不存在，或者为了避免使用同一个元素两次（虽然题目说每种输入只会对应一个答案，但考虑更一般的情况），可以将当前数字 `num` 和其下标 `i` 存入哈希表 `seen` 中，供后续的元素查找。

* **Go语言实现**：
    ```go
    package main

    import "fmt"

    func twoSum_hashMap(nums []int, target int) []int {
        seen := make(map[int]int) // value -> index
        for i, num := range nums {
            complement := target - num
            if index, ok := seen[complement]; ok {
                return []int{index, i}
            }
            seen[num] = i // 将当前数字及其下标存入哈希表
        }
        return nil // 题目保证有解
    }

    func main() {
        nums := []int{2, 7, 11, 15}
        target := 9
        fmt.Println(twoSum_hashMap(nums, target)) // Output: [0, 1]

        nums2 := []int{3, 2, 4}
        target2 := 6
        fmt.Println(twoSum_hashMap(nums2, target2)) // Output: [1, 2]

        nums3 := []int{3, 3}
        target3 := 6
        fmt.Println(twoSum_hashMap(nums3, target3)) // Output: [0, 1]
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N)$，其中 $N$ 是数组的长度。我们只需要遍历数组一次，哈希表的插入和查找操作平均时间复杂度为 $O(1)$。
    * 空间复杂度：$O(N)$，在最坏的情况下，哈希表可能需要存储数组中所有的 $N$ 个元素。

* **优点**：时间复杂度显著降低，是解决此类问题的最优方案。

### 1.3 总结题目对应的解题模式

* **哈希表查找 (Hash Table Lookup)**:
    这道题的核心模式是利用哈希表（或字典、映射）来优化查找过程。当我们需要快速判断一个元素是否存在于一个集合中，或者需要查找一个“配对”元素时，哈希表通常是首选的数据结构。
    其基本思路是：**用空间换时间**。通过将元素及其相关信息（如下标）存储在哈希表中，可以将后续的查找时间从 $O(N)$（线性搜索）或 $O(\log N)$（如果排序后二分搜索）降低到平均 $O(1)$。

### 1.4 泛化类似的问题

使用哈希表进行快速查找的模式可以应用于许多其他问题：

1.  **K-Sum 问题**：如 "3Sum"、"4Sum"，寻找数组中 K 个数之和为目标值。通常可以通过固定 K-1 或 K-2 个数，然后将问题转化为 "Two Sum" 或类似问题，并用哈希表加速查找。
2.  **寻找特定差值的数对**：给定一个数组和一个差值 `diff`，找出是否存在两个数 `a` 和 `b` 使得 `a - b = diff`。可以转换为查找 `a - diff` 或 `b + diff`。
3.  **无重复字符的最长子串 (Longest Substring Without Repeating Characters)**：哈希表可以用来存储字符及其最近出现的位置，以快速判断字符是否在当前窗口内重复。
4.  **子数组/子序列和问题**：如 "Subarray Sum Equals K"，可以用哈希表存储前缀和及其出现的次数或位置，以快速判断是否存在某个区间的和等于目标值。
5.  **两数之和 II - 输入有序数组 (Two Sum II - Input array is sorted)**：虽然这道题由于数组有序，可以使用双指针法达到 $O(N)$ 时间复杂度和 $O(1)$ 空间复杂度，但如果忽略有序条件，哈希表方法依然适用。
6.  **检查数组中是否存在重复元素 (Contains Duplicate)**：遍历数组，将元素存入哈希集合（`map[int]bool` 或 `map[int]struct{}`），如果尝试存入已存在的元素，则说明有重复。

这种“以空间换时间，通过哈希表进行快速查找”的思维模式是算法设计中非常重要和常用的一种技巧。

---

接下来是 **Contains Duplicate**。

## 2. Contains Duplicate (存在重复元素)

### 2.1 理解题目

* **题目描述**：给你一个整数数组 `nums`。如果任一数值在数组中出现至少两次，返回 `true`；如果数组中每个元素互不相同，返回 `false`。
* **主要考察点**：哈希集合（或哈希表）的使用、排序。
* **目的**：考察对数据集中重复元素的检测能力。

### 2.2 从思路引出最优解决方案 (Go语言实现)

#### 思路一：暴力枚举 (Brute Force)

最简单的方法是比较数组中每一对元素。

* **步骤**：
    1.  使用外层循环遍历数组中的每个元素 `nums[i]`。
    2.  使用内层循环遍历 `nums[i]` 之后的所有元素 `nums[j]`。
    3.  如果 `nums[i] == nums[j]`，则说明存在重复元素，返回 `true`。
    4.  如果所有循环结束后都没有找到相等的元素对，则返回 `false`。

* **Go语言实现**：
    ```go
    package main

    import "fmt"

    func containsDuplicate_bruteForce(nums []int) bool {
        n := len(nums)
        for i := 0; i < n; i++ {
            for j := i + 1; j < n; j++ {
                if nums[i] == nums[j] {
                    return true
                }
            }
        }
        return false
    }

    func main() {
        fmt.Println(containsDuplicate_bruteForce([]int{1, 2, 3, 1})) // true
        fmt.Println(containsDuplicate_bruteForce([]int{1, 2, 3, 4})) // false
        fmt.Println(containsDuplicate_bruteForce([]int{1, 1, 1, 3, 3, 4, 3, 2, 4, 2})) // true
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N^2)$，其中 $N$ 是数组的长度。
    * 空间复杂度：$O(1)$。

* **缺点**：时间复杂度较高，不适用于大规模数据。

#### 思路二：排序 (Sorting)

如果数组是有序的，那么任何重复的元素都会相邻。

* **步骤**：
    1.  对数组 `nums` 进行排序。
    2.  遍历排序后的数组，从第二个元素开始（下标为 1）。
    3.  比较当前元素 `nums[i]` 和它前一个元素 `nums[i-1]`。
    4.  如果 `nums[i] == nums[i-1]`，则说明存在重复元素，返回 `true`。
    5.  如果遍历完成都没有找到相等的相邻元素，则返回 `false`。

* **Go语言实现**：
    ```go
    package main

    import (
        "fmt"
        "sort"
    )

    func containsDuplicate_sorting(nums []int) bool {
        sort.Ints(nums) // 对数组进行排序
        n := len(nums)
        for i := 1; i < n; i++ {
            if nums[i] == nums[i-1] {
                return true
            }
        }
        return false
    }

    func main() {
        fmt.Println(containsDuplicate_sorting([]int{1, 2, 3, 1})) // true
        fmt.Println(containsDuplicate_sorting([]int{1, 2, 3, 4})) // false
        fmt.Println(containsDuplicate_sorting([]int{1, 1, 1, 3, 3, 4, 3, 2, 4, 2})) // true
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N \log N)$，主要取决于排序算法的时间复杂度。Go 的 `sort.Ints` 通常是基于快速排序的变种，平均时间复杂度是 $O(N \log N)$。
    * 空间复杂度：$O(\log N)$ 或 $O(N)$，取决于排序算法的实现。Go 的 `sort.Ints` 是原地排序，但递归栈可能消耗 $O(\log N)$ 空间；某些排序算法可能需要 $O(N)$ 的辅助空间。如果允许修改原数组，则可以认为是 $O(\log N)$ (栈空间)。

* **优点**：相比暴力法，时间效率有显著提升。
* **缺点**：修改了原数组（如果不能修改，则需要拷贝一份，空间复杂度变为 $O(N)$）。

#### 思路三：哈希集合 (Hash Set)

我们可以使用哈希集合（在 Go 中可以用 `map[int]struct{}` 或 `map[int]bool` 实现）来存储已经遇到过的元素。

* **步骤**：
    1.  创建一个空的哈希集合 `seen`。
    2.  遍历数组 `nums` 中的每个元素 `num`。
    3.  检查 `num` 是否已经存在于 `seen` 中。
        a.  如果存在，说明遇到了重复元素，返回 `true`。
        b.  如果不存在，将 `num` 添加到 `seen` 中。
    4.  如果遍历完成都没有在哈希集合中找到已存在的元素，则返回 `false`。

* **Go语言实现**：
    ```go
    package main

    import "fmt"

    func containsDuplicate_hashSet(nums []int) bool {
        seen := make(map[int]struct{}) // 使用空结构体节省空间
        for _, num := range nums {
            if _, ok := seen[num]; ok { // 检查元素是否已在集合中
                return true
            }
            seen[num] = struct{}{} // 将元素添加到集合中
        }
        return false
    }

    func main() {
        fmt.Println(containsDuplicate_hashSet([]int{1, 2, 3, 1})) // true
        fmt.Println(containsDuplicate_hashSet([]int{1, 2, 3, 4})) // false
        fmt.Println(containsDuplicate_hashSet([]int{1, 1, 1, 3, 3, 4, 3, 2, 4, 2})) // true
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N)$，因为我们遍历数组一次，哈希集合的插入和查找操作平均时间复杂度为 $O(1)$。
    * 空间复杂度：$O(N)$，在最坏情况下（所有元素都不同），哈希集合需要存储所有 $N$ 个元素。

* **优点**：时间复杂度最优，且不修改原数组。
* **权衡**：哈希集合法用空间换取了时间。如果空间限制非常严格，且可以修改原数组，排序法可能是更好的选择。否则，哈希集合法通常是首选。

### 2.3 总结题目对应的解题模式

* **哈希集合判重 (Hash Set for Uniqueness/Duplicates)**:
    当需要检查一个集合中是否存在重复元素，或者需要统计元素的唯一性时，哈希集合是一个非常高效的工具。通过将元素逐个添加到哈希集合中，并利用其 $O(1)$ 的平均查找时间来判断元素是否已经存在。
* **排序后检查相邻元素 (Sort and Check Adjacent)**:
    对于一些涉及元素相对大小或重复性的问题，排序是一个常见的预处理步骤。排序后，相同的元素会聚集在一起，使得重复检查变得简单（只需比较相邻元素）。

### 2.4 泛化类似的问题

1.  **查找第一个不重复的字符 (First Unique Character in a String)**：可以使用哈希表记录字符出现的频率，或者记录字符首次出现的位置，然后再次遍历找到符合条件的。
2.  **两个数组的交集/并集 (Intersection/Union of Two Arrays)**：可以将一个数组的元素存入哈希集合，然后遍历另一个数组，检查元素是否存在于哈希集合中。
3.  **有效的数独 (Valid Sudoku)**：需要分别检查行、列、3x3子九宫格内是否有重复数字。对每个单元（行、列、子九宫格）可以使用哈希集合来判重。
4.  **快乐数 (Happy Number)**：在计算过程中，如果出现重复的数字，说明进入了循环，不是快乐数。可以用哈希集合记录出现过的数字。
5.  **最长连续序列 (Longest Consecutive Sequence)**：可以将所有数字存入哈希集合，然后遍历数字，对于每个数字 `x`，如果 `x-1` 不在集合中，则 `x` 是一个潜在序列的开始，然后不断检查 `x+1`, `x+2`... 是否在集合中来确定序列长度。

这些问题都利用了哈希数据结构快速查找或标记已见元素的特性。

---

下一个是 **Best Time to Buy and Sell Stock**。

## 3. Best Time to Buy and Sell Stock (买卖股票的最佳时机)

### 3.1 理解题目

* **题目描述**：给定一个数组 `prices`，它的第 `i` 个元素 `prices[i]` 表示一支给定股票在第 `i` 天的价格。你只能选择 **某一天** 买入这只股票，并选择在 **未来的某一个不同的日子** 卖出该股票。设计一个算法来计算你所能获取的最大利润。如果你不能获取任何利润，返回 0。
* **主要考察点**：数组遍历、动态规划（简单形式）、状态维护。
* **目的**：在一次遍历中找到最大差值（卖出价 - 买入价），其中买入价必须在卖出价之前。

### 3.2 从思路引出最优解决方案 (Go语言实现)

#### 思路一：暴力枚举 (Brute Force)

尝试所有可能的买入和卖出组合。

* **步骤**：
    1.  使用外层循环遍历每一天 `i` 作为买入日。
    2.  使用内层循环遍历 `i` 之后的所有天 `j` 作为卖出日。
    3.  计算利润 `prices[j] - prices[i]`。
    4.  维护一个最大利润 `maxProfit`，不断更新它。
    5.  如果最终 `maxProfit` 小于0，则返回0，否则返回 `maxProfit`。

* **Go语言实现**：
    ```go
    package main

    import (
        "fmt"
        "math"
    )

    func maxProfit_bruteForce(prices []int) int {
        maxProfit := 0
        n := len(prices)
        if n < 2 {
            return 0
        }

        for i := 0; i < n-1; i++ { // 买入日
            for j := i + 1; j < n; j++ { // 卖出日
                profit := prices[j] - prices[i]
                if profit > maxProfit {
                    maxProfit = profit
                }
            }
        }
        return maxProfit
    }

    func main() {
        fmt.Println(maxProfit_bruteForce([]int{7, 1, 5, 3, 6, 4})) // 5 (buy at 1, sell at 6)
        fmt.Println(maxProfit_bruteForce([]int{7, 6, 4, 3, 1}))    // 0 (no transaction)
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N^2)$，其中 $N$ 是天数（数组长度）。
    * 空间复杂度：$O(1)$。

* **缺点**：时间复杂度较高。

#### 思路二：一次遍历 (One Pass / Greedy Approach)

我们希望以尽可能低的价格买入，并以尽可能高的价格卖出。关键在于，卖出日必须在买入日之后。

* **步骤**：
    1.  初始化 `minPrice` 为一个非常大的数（或者数组的第一个元素），表示到目前为止遇到的最低股价。
    2.  初始化 `maxProfit` 为 0，表示到目前为止的最大利润。
    3.  遍历股价数组 `prices`：
        a.  对于当天的价格 `price`：
            i.  如果 `price` 比 `minPrice` 更低，那么更新 `minPrice = price`。因为在未来，如果以这个更低的价格买入，可能会获得更高利润。
            ii. 如果用当前价格 `price` 卖出，并且买入价格是之前记录的 `minPrice`，那么计算当前利润 `profit = price - minPrice`。
            iii. 如果这个 `profit` 大于当前的 `maxProfit`，则更新 `maxProfit = profit`。
    4.  遍历结束后，`maxProfit` 就是所求的最大利润。

* **逻辑解释**：
    在遍历到第 `i` 天时，我们假设在今天卖出股票。为了获得最大利润，我们应该在第 `0` 到 `i-1` 天中价格最低的那一天买入。所以，我们只需要在遍历过程中动态地维护一个历史最低价格 `minPrice`。
    当天的价格 `prices[i]` 减去 `minPrice` 就是在今天卖出可能获得的最大利润。我们用这个值去更新全局的 `maxProfit`。同时，我们也用 `prices[i]` 去更新 `minPrice`，以便后续的计算。

* **Go语言实现**：
    ```go
    package main

    import (
        "fmt"
        "math"
    )

    func maxProfit_onePass(prices []int) int {
        if len(prices) < 2 {
            return 0
        }

        minPrice := math.MaxInt32 // 初始化为最大整数
        maxProfit := 0

        for _, price := range prices {
            if price < minPrice {
                minPrice = price // 更新最低买入价
            } else if price-minPrice > maxProfit {
                maxProfit = price - minPrice // 更新最大利润
            }
        }
        return maxProfit
    }

    func main() {
        fmt.Println(maxProfit_onePass([]int{7, 1, 5, 3, 6, 4})) // 5
        fmt.Println(maxProfit_onePass([]int{7, 6, 4, 3, 1}))    // 0
        fmt.Println(maxProfit_onePass([]int{1, 2}))             // 1
        fmt.Println(maxProfit_onePass([]int{2, 1}))             // 0
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N)$，因为我们只遍历数组一次。
    * 空间复杂度：$O(1)$，只使用了常数级别的额外空间。

* **优点**：时间复杂度最优，非常高效。

### 3.3 总结题目对应的解题模式

* **单次遍历状态维护 (Single Pass with State Maintenance)** / **贪心思想 (Greedy Approach)**:
    这种模式的核心是在一次遍历过程中，通过维护一些关键状态变量（如本题中的 `minPrice` 和 `maxProfit`），并根据当前元素和已维护的状态来做出决策或更新结果。
    对于这道题，贪心体现在：在任何一天考虑卖出时，我们总是希望买入价格是此前出现的最低价格。
    也可以看作一种简单的动态规划：`dp[i]` 表示到第 `i` 天为止的最大利润。但实际上我们不需要一个 `dp` 数组，只需要两个变量来记录状态。
    `maxProfit` = $\max(\text{maxProfit}, \text{prices}[i] - \text{minPriceSoFar})$
    `minPriceSoFar` = $\min(\text{minPriceSoFar}, \text{prices}[i])$

### 3.4 泛化类似的问题

1.  **最大子数组和 (Maximum Subarray)** (Kadane's Algorithm)：与此题思路相似，也是在一次遍历中维护当前子数组的和以及全局最大子数组的和。
2.  **寻找数组中的峰值 (Find Peak Element)**：虽然不完全相同，但也涉及到在遍历中根据局部信息更新全局最优（或满足条件的元素）。
3.  **需要记录“至今为止最优”的问题**：许多问题要求在遍历序列时，根据当前元素和“到目前为止”的某个最优值（最小值、最大值、最优组合等）来更新最终答案。
    * 例如，在一个序列中找到相差最大的两个元素，且较小元素必须在较大元素之前（这就是本题）。
    * 或者，找到一个最长的递增子序列（LIS），虽然LIS的经典解法是 $O(N \log N)$ 的DP，但其思想也包含维护某种“最优状态”。
4.  **股票买卖系列问题**：
    * **Best Time to Buy and Sell Stock II** (可以多次买卖)：贪心，只要今天比昨天价格高就交易。
    * **Best Time to Buy and Sell Stock III** (最多两次买卖)：更复杂的动态规划。
    * **Best Time to Buy and Sell Stock IV** (最多k次买卖)：更复杂的动态规划。
    * **Best Time to Buy and Sell Stock with Cooldown**：动态规划，需要考虑冷冻期。
    * **Best Time to Buy and Sell Stock with Transaction Fee**：动态规划，考虑交易费用。
    这些问题虽然更复杂，但都基于“在某个时间点做决策（买、卖、持有、冷冻）以最大化利润”的核心思想，而本题是这个系列最简单的一种情况。

---

下一个是 **Valid Anagram**。

## 4. Valid Anagram (有效的字母异位词)

### 4.1 理解题目

* **题目描述**：给定两个字符串 `s` 和 `t`，编写一个函数来判断 `t` 是否是 `s` 的字母异位词。所谓字母异位词是指两个字符串包含完全相同的字母，只是字母的顺序不同。
* **假设**：你可以假设字符串只包含小写字母。
* **主要考察点**：字符串处理、哈希表（字符计数）、排序。
* **目的**：判断两个集合的元素组成是否完全相同。

### 4.2 从思路引出最优解决方案 (Go语言实现)

#### 思路一：排序 (Sorting)

如果两个字符串是字母异位词，那么将它们各自排序后，得到的字符串应该是完全相同的。

* **步骤**：
    1.  检查两个字符串的长度是否相等。如果不相等，它们肯定不是字母异位词，直接返回 `false`。
    2.  将字符串 `s` 转换为字符数组，并对其进行排序。
    3.  将字符串 `t` 转换为字符数组，并对其进行排序。
    4.  比较排序后的两个字符数组（或转换回字符串后比较）。如果它们相同，则返回 `true`，否则返回 `false`。

* **Go语言实现**：
    ```go
    package main

    import (
        "fmt"
        "sort"
        "strings"
    )

    // Helper function to sort a string
    func sortString(s string) string {
        chars := strings.Split(s, "")
        sort.Strings(chars)
        return strings.Join(chars, "")
    }

    func isAnagram_sorting(s string, t string) bool {
        if len(s) != len(t) {
            return false
        }
        return sortString(s) == sortString(t)
    }

    func main() {
        fmt.Println(isAnagram_sorting("anagram", "nagaram")) // true
        fmt.Println(isAnagram_sorting("rat", "car"))         // false
        fmt.Println(isAnagram_sorting("listen", "silent"))   // true
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N \log N)$，其中 $N$ 是字符串的长度。主要开销在于排序。字符串转换和比较的时间复杂度是 $O(N)$。
    * 空间复杂度：$O(N)$ 或 $O(\log N)$，取决于排序算法的空间使用以及字符串到字符数组的转换是否创建副本。Go的 `strings.Split` 和 `strings.Join` 会创建新字符串/切片，排序本身也可能需要额外空间。如果字符集固定（如小写字母），可以用 $O(1)$ 的计数排序，但通用排序是 $N \log N$。此处 `sort.Strings` 对字符串切片排序，每个字符串复制可能导致 $O(N)$ 空间。

* **优点**：思路简单直观。
* **缺点**：排序的开销相对较大。

#### 思路二：哈希表/字符计数 (Hash Table / Frequency Counter)

字母异位词的定义意味着两个字符串中每个字符出现的次数都必须完全相同。我们可以用一个哈希表（或一个固定大小的数组，如果字符集有限且已知，比如26个小写字母）来统计第一个字符串中各字符的频率，然后用第二个字符串来核对这些频率。

* **步骤**：
    1.  检查两个字符串的长度是否相等。如果不相等，直接返回 `false`。
    2.  创建一个哈希表 `charCount` (或一个大小为26的整数数组，索引对应 'a' 到 'z')。
    3.  遍历字符串 `s`，对于每个字符 `c`：
        * 增加 `charCount[c]` 的计数。
    4.  遍历字符串 `t`，对于每个字符 `c`：
        * 减少 `charCount[c]` 的计数。
        * 如果在减少之前 `charCount[c]` 已经是0（或者减少后变成负数），说明 `t` 中某个字符的数量多于 `s` 中的数量（或者 `t` 中出现了 `s` 中没有的字符，前提是先用 `s` 初始化频率都为正），则返回 `false`。
    5.  遍历完成后，再次检查 `charCount` 中的所有计数。如果所有计数都为0，则返回 `true`。否则返回 `false`。（如果步骤1保证长度相等，且步骤4中每次都有效减少了计数，那么这一步可以省略，因为所有计数必然为0）。

* **Go语言实现 (使用大小为26的数组)**：
    ```go
    package main

    import "fmt"

    func isAnagram_charCount(s string, t string) bool {
        if len(s) != len(t) {
            return false
        }

        charCount := make([]int, 26) // 假设只有小写字母

        // 统计字符串 s 中每个字符的频率
        for _, char := range s {
            charCount[char-'a']++
        }

        // 核对字符串 t 中的字符频率
        for _, char := range t {
            charCount[char-'a']--
            // 如果某个字符在 t 中出现的次数多于 s，或者 t 中有 s 中没有的字符
            // 那么对应计数会变成负数
            if charCount[char-'a'] < 0 {
                return false
            }
        }

        // 理论上，如果长度相同且上面检查通过，所有计数都应为0
        // 可以选择不检查这一步，或者作为保险：
        // for _, count := range charCount {
        //     if count != 0 {
        //         return false
        //     }
        // }

        return true
    }

    func main() {
        fmt.Println(isAnagram_charCount("anagram", "nagaram")) // true
        fmt.Println(isAnagram_charCount("rat", "car"))         // false
        fmt.Println(isAnagram_charCount("listen", "silent"))   // true
        fmt.Println(isAnagram_charCount("aacc", "ccac"))       // false (预期 false, 因为长度相同，但字符'a'在s中2次, t中1次；c在s中2次, t中3次 -- 啊，我的例子应该是 true, 改下例子)
        fmt.Println(isAnagram_charCount("aabb", "bbaa"))       // true
        fmt.Println(isAnagram_charCount("a", "ab"))            // false (长度不同，会提前返回)
    }
    ```
    如果字符集不限于小写字母，可以使用 `map[rune]int`：
    ```go
    package main

    import "fmt"

    func isAnagram_charCountMap(s string, t string) bool {
        if len(s) != len(t) {
            return false
        }

        charCount := make(map[rune]int)

        for _, char := range s {
            charCount[char]++
        }

        for _, char := range t {
            charCount[char]--
            if charCount[char] < 0 {
                return false
            }
        }
        // No need to check if all counts are zero if lengths are equal
        // and all decrements were valid.
        return true
    }
    // main function would be similar
    ```


* **复杂度分析 (使用固定大小数组)**：
    * 时间复杂度：$O(N)$，其中 $N$ 是字符串的长度。我们遍历两个字符串各一次。数组的访问是 $O(1)$。
    * 空间复杂度：$O(1)$，因为字符集的大小是固定的（例如26个小写字母），所以计数数组的大小是常数。如果使用 `map[rune]int`，空间复杂度是 $O(S)$，其中 $S$ 是字符集的大小（比如 `s` 和 `t` 中不同字符的总数，最坏情况下是 $N$）。对于Unicode字符，字符集可能很大。

* **优点**：时间复杂度最优。对于有限字符集，空间复杂度也是最优的。

### 4.3 总结题目对应的解题模式

* **字符计数 / 频率统计 (Character Counting / Frequency Map)**:
    这是解决涉及字符排列、组合或比较内容（而非顺序）的问题的常用模式。通过统计每个字符出现的次数，我们可以忽略它们的顺序，直接比较它们的组成。
    这种模式可以使用哈希表（如 Go 中的 `map`）或者在字符集较小且固定时使用数组（例如ASCII或小写字母）。

### 4.4 泛化类似的问题

1.  **字母异位词分组 (Group Anagrams)**：这是本题的扩展，需要将一个字符串列表中的所有字母异位词分到同一组。可以使用排序后的字符串或字符计数数组/哈希表的规范表示作为分组的键。
2.  **最短窗口子串 (Minimum Window Substring)**：需要在字符串S中找到包含字符串T所有字符的最短子串。需要用哈希表统计T中字符的频率，然后在S中滑动窗口，维护窗口内字符频率。
3.  **排列的包含 (Permutation in String)**：判断字符串 `s1` 的排列之一是否是字符串 `s2` 的子串。可以用字符计数和滑动窗口。
4.  **查找字符串中所有字母异位词 (Find All Anagrams in a String)**：给定字符串 `s` 和 `p`，找出 `s` 中所有 `p` 的字母异位词的起始索引。同样使用字符计数和滑动窗口。
5.  **回文排列 (Palindrome Permutation)**：判断一个字符串是否能通过重新排列字符形成一个回文字符串。关键在于统计字符频率：最多只能有一个字符的出现次数为奇数。

这些问题都受益于对字符串内字符频率的精确统计和比较。

---

接下来是 **Valid Parentheses**。

## 5. Valid Parentheses (有效的括号)

### 5.1 理解题目

* **题目描述**：给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串 `s`，判断字符串是否有效。
* **有效字符串需满足**：
    1.  左括号必须用相同类型的右括号闭合。
    2.  左括号必须以正确的顺序闭合。
    3.  每个右括号都有一个对应的相同类型的左括号。
* **主要考察点**：栈数据结构的应用。
* **目的**：考察是否能理解和运用栈的“后进先出”（LIFO）特性来解决匹配问题。

### 5.2 从思路引出最优解决方案 (Go语言实现)

#### 思路：使用栈 (Stack)

括号匹配问题是栈的经典应用场景。栈的“后进先出”特性非常适合处理这种嵌套结构的匹配。

* **步骤**：
    1.  创建一个空栈。
    2.  创建一个哈希表 `mapping`，用于存储右括号到对应左括号的映射，例如 `mapping = {')': '(', '}': '{', ']': '['}`。
    3.  遍历输入字符串 `s` 中的每个字符 `char`：
        a.  **如果 `char` 是一个左括号** (`'('`, `'{'`, `'['`)：将其压入栈中。
        b.  **如果 `char` 是一个右括号** (`')'`, `'}'`, `']'`)：
            i.  检查栈是否为空。如果栈为空，说明没有对应的左括号来匹配当前的右括号，字符串无效，返回 `false`。
            ii. 如果栈不为空，从栈顶弹出一个元素 `topElement`。
            iii. 检查 `topElement` 是否是当前右括号 `char` 对应的左括号（即 `mapping[char] == topElement`）。如果不是，说明括号类型不匹配，字符串无效，返回 `false`。
    4.  遍历完整个字符串后：
        a.  如果栈为空，说明所有的左括号都找到了对应的右括号并成功匹配，字符串有效，返回 `true`。
        b.  如果栈不为空，说明还有未闭合的左括号，字符串无效，返回 `false`。

* **Go语言实现**：
    在 Go 中，没有内置的栈类型，通常使用切片（slice）来模拟栈的行为：
    * 压栈：`stack = append(stack, element)`
    * 弹栈：`element = stack[len(stack)-1]; stack = stack[:len(stack)-1]`
    * 检查栈顶：`element = stack[len(stack)-1]`
    * 检查栈空：`len(stack) == 0`

    ```go
    package main

    import "fmt"

    func isValidParentheses(s string) bool {
        stack := []rune{} // 使用 rune 切片作为栈，处理 Unicode 字符更安全
        mapping := map[rune]rune{
            ')': '(',
            '}': '{',
            ']': '[',
        }

        for _, char := range s {
            switch char {
            case '(', '{', '[': // 如果是左括号，压栈
                stack = append(stack, char)
            case ')', '}', ']': // 如果是右括号
                if len(stack) == 0 { // 栈为空，没有匹配的左括号
                    return false
                }
                topElement := stack[len(stack)-1]
                stack = stack[:len(stack)-1] // 弹栈

                if mapping[char] != topElement { // 括号类型不匹配
                    return false
                }
            default:
                // 题目说明只包含括号字符，可以不处理此情况
                // 如果可能包含其他字符，则应忽略或报错
                return false // 或者 continue; 取决于题目要求
            }
        }

        // 如果遍历结束后栈为空，则有效
        return len(stack) == 0
    }

    func main() {
        fmt.Println(isValidParentheses("()"))     // true
        fmt.Println(isValidParentheses("()[]{}")) // true
        fmt.Println(isValidParentheses("(]"))     // false
        fmt.Println(isValidParentheses("([)]"))   // false
        fmt.Println(isValidParentheses("{[]}"))   // true
        fmt.Println(isValidParentheses("["))      // false
        fmt.Println(isValidParentheses("]"))      // false
        fmt.Println(isValidParentheses(""))       // true (空字符串是有效的)
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N)$，其中 $N$ 是字符串的长度。因为我们只遍历字符串一次，每个字符的栈操作（压栈、弹栈）都是 $O(1)$ 的时间复杂度。
    * 空间复杂度：$O(N)$，在最坏的情况下（例如字符串全是左括号 `((((...`），栈的大小会增长到 $N$。

* **优点**：思路清晰，实现直接，是解决此类匹配问题的标准且高效的方法。

### 5.3 总结题目对应的解题模式

* **栈进行序列匹配/平衡检查 (Stack for Sequence Matching / Balancing)**:
    当处理需要匹配成对出现、且具有嵌套关系的元素（如括号、XML/HTML标签等）时，栈是非常有效的数据结构。
    基本模式：
    1.  遇到“开”符号，压入栈中。
    2.  遇到“关”符号，检查栈顶元素是否是对应的“开”符号。
        * 是，则弹出栈顶，表示一对成功匹配。
        * 否，或栈为空，则表示不匹配或不平衡。
    3.  序列处理完毕后，栈应为空，才表示所有“开”符号都已正确关闭。

### 5.4 泛化类似的问题

1.  **简化路径 (Simplify Path)**：处理Unix路径字符串，涉及到 `.`（当前目录）、`..`（上一级目录）和多个 `/`。可以用栈来模拟目录的进入和退出。
2.  **基本计算器系列 (Basic Calculator I, II, III)**：解析并计算包含加减乘除和括号的算术表达式。通常需要两个栈：一个操作数栈，一个操作符栈，或者通过特定算法（如逆波兰表达式转换或双栈法）来处理优先级和括号。
3.  **下一个更大元素 (Next Greater Element I, II)**：对于数组中的每个元素，找到其右侧第一个比它大的元素。可以使用单调栈。
4.  **最小栈 (Min Stack)**：设计一个支持 `push`, `pop`, `top`, 和在常数时间内检索最小元素的栈。通常需要辅助栈或在每个元素入栈时存储当前的最小值。
5.  **每日温度 (Daily Temperatures)**：给定一个每日温度列表，对于每一天，你必须计算出还要等多久才能出现更高的温度。可以用单调栈存储日期的索引。
6.  **HTML/XML 标签验证**：与括号匹配类似，开始标签入栈，遇到结束标签时检查栈顶是否是对应的开始标签。

这些问题都利用了栈的 LIFO 特性来管理和匹配具有顺序和嵌套关系的元素或状态。

---

下一个是 **Maximum Subarray**。

## 6. Maximum Subarray (最大子数组和)

### 6.1 理解题目

* **题目描述**：给你一个整数数组 `nums`，请你找出一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。
* **子数组**：是数组中的一个连续部分。
* **主要考察点**：动态规划 (Kadane's Algorithm)、分治法。
* **目的**：在数组中找到一段连续的元素，使得它们的和最大。

### 6.2 从思路引出最优解决方案 (Go语言实现)

#### 思路一：暴力枚举 (Brute Force)

枚举所有可能的连续子数组，计算它们的和，然后找出最大的那个。

* **步骤**：
    1.  使用外层循环确定子数组的起始位置 `i`。
    2.  使用内层循环确定子数组的结束位置 `j`（其中 `j >= i`）。
    3.  计算从 `nums[i]` 到 `nums[j]` 的和。
    4.  维护一个全局最大和 `maxSum`，不断更新。

* **Go语言实现**：
    ```go
    package main

    import (
        "fmt"
        "math"
    )

    func maxSubArray_bruteForce(nums []int) int {
        n := len(nums)
        if n == 0 {
            return 0 // Or handle as an error, though problem constraints usually ensure non-empty
        }
        maxSum := math.MinInt32 // Initialize with a very small number

        for i := 0; i < n; i++ {
            currentSum := 0
            for j := i; j < n; j++ {
                currentSum += nums[j]
                if currentSum > maxSum {
                    maxSum = currentSum
                }
            }
        }
        return maxSum
    }

    func main() {
        fmt.Println(maxSubArray_bruteForce([]int{-2, 1, -3, 4, -1, 2, 1, -5, 4})) // 6 ([4,-1,2,1])
        fmt.Println(maxSubArray_bruteForce([]int{1}))                             // 1
        fmt.Println(maxSubArray_bruteForce([]int{5, 4, -1, 7, 8}))                 // 23 ([5,4,-1,7,8])
        fmt.Println(maxSubArray_bruteForce([]int{-1, -2, -3}))                    // -1 ([-1])
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N^2)$。外层循环 $N$ 次，内层循环平均 $N/2$ 次，计算和的操作在内层循环中累加。
    * 空间复杂度：$O(1)$。

* **缺点**：时间复杂度较高。

#### 思路二：动态规划 (Kadane's Algorithm)

这是解决此问题的经典算法。核心思想是：对于数组中的每个位置 `i`，我们要计算以 `nums[i]` **结尾**的连续子数组的最大和。

* **定义状态**：
    `dp[i]`：表示以 `nums[i]` 结尾的连续子数组的最大和。

* **状态转移方程**：
    考虑以 `nums[i]` 结尾的子数组，它有两种可能：
    1.  它只包含 `nums[i]` 本身。
    2.  它包含 `nums[i]` 以及 `nums[i]` 前面的一段子数组。这段前面的子数组必须是以 `nums[i-1]` 结尾的。
    所以，`dp[i] = nums[i] + max(dp[i-1], 0)`。更简洁地写是 `dp[i] = nums[i] + (if dp[i-1] > 0 then dp[i-1] else 0)` 或者 `dp[i] = max(nums[i], nums[i] + dp[i-1])`。
    * 如果 `dp[i-1]` (以 `nums[i-1]` 结尾的最大子数组和) 是正数，那么 `dp[i]` 就应该接上它，即 `nums[i] + dp[i-1]`。
    * 如果 `dp[i-1]` 是负数或零，那么 `dp[i]` 还不如从 `nums[i]` 自身开始一个新的子数组，即 `nums[i]`。

* **最终结果**：
    题目要求的是整个数组中任意连续子数组的最大和，而不是必须以某个特定元素结尾。所以，最终结果是所有 `dp[i]` 值中的最大值。
    `maxGlobalSum = max(dp[0], dp[1], ..., dp[n-1])`。

* **空间优化**：
    注意到 `dp[i]` 的计算只依赖于 `dp[i-1]`。所以我们不需要一个完整的 `dp` 数组，只需要一个变量来存储前一个状态 `dp[i-1]` (即 `currentMaxEndingHere`)，以及一个变量来存储全局最大和 `maxSoFar`。

* **步骤 (优化后)**：
    1.  初始化 `currentMaxEndingHere = 0` (或者 `nums[0]`，如果从第一个元素开始处理)。
    2.  初始化 `maxSoFar = nums[0]` (或者一个非常小的数，如 `math.MinInt32`)。
    3.  遍历数组 `nums` (从第一个或第二个元素开始，取决于初始化)：
        a.  对于当前元素 `num`：
            `currentMaxEndingHere = max(num, currentMaxEndingHere + num)`
            (或者，更直观的：`currentMaxEndingHere = currentMaxEndingHere + num`。如果 `currentMaxEndingHere < num` 并且 `num` 本身比累加和要大，说明之前的累加是负贡献，不如从 `num` 重新开始。所以，`currentMaxEndingHere = max(num, currentMaxEndingHere + num)` 是更本质的。
            另一种理解：`currentMaxEndingHere = currentMaxEndingHere + num`。 如果 `currentMaxEndingHere` 变为负数，说明以当前元素结尾的子序列如果继续向后延伸，这个负的 `currentMaxEndingHere` 将会拖累后续的和，不如从下一个元素重新开始计算。所以，如果 `currentMaxEndingHere < 0`，则将其重置为 `0` (表示下一个元素将开始一个新的子数组)。这里用 `max(num, currentMaxEndingHere + num)` 更为通用和简洁。)
            * `currentMaxEndingHere` 更新为：包含当前元素 `num` 的情况下，到目前为止能得到的最大子数组和。它可以是 `num` 自己，也可以是 `num` 加上之前以 `num` 前一个元素结尾的最大子数组和。
                `currentMaxEndingHere = max(num, currentMaxEndingHere + num)`。
            * 另一种 Kadane 实现方式：
                `currentSum = currentSum + num`
                `if currentSum > maxSoFar: maxSoFar = currentSum`
                `if currentSum < 0: currentSum = 0` (如果累加和为负，则丢弃它，从下一个元素重新开始计数)

        b.  更新全局最大和：`maxSoFar = max(maxSoFar, currentMaxEndingHere)`。
    4.  返回 `maxSoFar`。

* **Go语言实现 (Kadane's Algorithm)**：
    ```go
    package main

    import (
        "fmt"
        "math"
    )

    func maxSubArray_kadane(nums []int) int {
        if len(nums) == 0 {
            return 0 // Or handle error
        }

        maxSoFar := nums[0]            // 全局最大和，至少是第一个元素
        currentMaxEndingHere := nums[0] // 以当前元素结尾的最大子数组和

        for i := 1; i < len(nums); i++ {
            num := nums[i]
            // 决定是以当前元素开始新子数组，还是将当前元素附加到现有子数组
            if currentMaxEndingHere+num > num {
                currentMaxEndingHere = currentMaxEndingHere + num
            } else {
                currentMaxEndingHere = num
            }
            // currentMaxEndingHere = max(num, currentMaxEndingHere + num) // 等价的写法

            // 更新全局最大和
            if currentMaxEndingHere > maxSoFar {
                maxSoFar = currentMaxEndingHere
            }
            // maxSoFar = max(maxSoFar, currentMaxEndingHere) // 等价的写法
        }
        return maxSoFar
    }
    
    // Kadane's 另一种常见写法
    func maxSubArray_kadane_v2(nums []int) int {
        if len(nums) == 0 {
            return 0
        }
        maxSoFar := math.MinInt32 // 初始化为足够小的值
        currentSum := 0
        for _, num := range nums {
            currentSum += num
            if currentSum > maxSoFar {
                maxSoFar = currentSum
            }
            if currentSum < 0 { // 如果累加和为负，则对后续子数组无益，重置
                currentSum = 0
            }
        }
        // 如果所有数字都是负数，maxSoFar 会是0（如果用 currentSum=0 初始化）。
        // 例如 [-2, -1]。currentSum 第一次 -2, maxSoFar -2, currentSum 重置 0。
        // currentSum 第二次 -1, maxSoFar 仍是 -2 (因为 -1 < 0, currentSum=0, 但 maxSoFar=-2 > -1 是错的)
        // 需要注意全负数的情况。
        // Kadane_v2 需要修正：maxSoFar 初始化为 nums[0] 或 math.MinInt32，并且 currentSum 的更新逻辑要小心。
        // 一个更健壮的 Kadane v2:
        // maxSoFar = nums[0]
        // currentSum = 0
        // for _, num := range nums {
        //    currentSum += num
        //    if currentSum > maxSoFar { maxSoFar = currentSum }
        //    if currentSum < 0 { currentSum = 0 } // 这个如果数组全负数，会返回0，错误
        // }
        // 如果上面Kadane_v2的maxSoFar初始值为math.MinInt32，并且在 currentSum < 0 之后，依然在更新 maxSoFar，
        // 那么是可以处理全负数的。例如：
        // maxSoFar = math.MinInt32
        // currentSum = 0
        // for _, num := range nums:
        //     currentSum += num
        //     if currentSum > maxSoFar: // 即使currentSum是负数，但可能比maxSoFar大
        //         maxSoFar = currentSum
        //     if currentSum < 0:
        //         currentSum = 0 // 准备开始新的子序列
        // return maxSoFar;  // 这个版本对于全负数，例如 [-1],会返回0，因为-1 > maxSoFar, maxSoFar=-1; currentSum=-1, currentSum=0. return maxSoFar = -1.
                           // 对于 [-2, -1], num=-2: currentSum=-2, maxSoFar=-2, currentSum=0
                           // num=-1: currentSum=-1, maxSoFar=-1 (因为-1 > -2), currentSum=0.  return -1. 这个对了。

        // 所以，第一种 Kadane 实现 (max(num, currentMaxEndingHere + num)) 更为简洁和不易出错。
        return maxSoFar
    }


    func main() {
        fmt.Println(maxSubArray_kadane([]int{-2, 1, -3, 4, -1, 2, 1, -5, 4})) // 6
        fmt.Println(maxSubArray_kadane([]int{1}))                             // 1
        fmt.Println(maxSubArray_kadane([]int{5, 4, -1, 7, 8}))                 // 23
        fmt.Println(maxSubArray_kadane([]int{-1}))                            // -1
        fmt.Println(maxSubArray_kadane([]int{-2, -1}))                        // -1
    }
    ```

* **复杂度分析 (Kadane's Algorithm)**：
    * 时间复杂度：$O(N)$，我们只遍历数组一次。
    * 空间复杂度：$O(1)$，只使用了常数级别的额外空间。

* **优点**：时间复杂度和空间复杂度都是最优的。

#### 思路三：分治法 (Divide and Conquer)

虽然 Kadane 算法已经是最优的，但这个问题也可以用分治法解决，这是一种不同的思考角度。

* **步骤**：
    1.  **基本情况**：如果数组只有一个元素，则最大子数组和就是该元素本身。
    2.  **分解**：将数组从中间分成两半：左半部分和右半部分。
    3.  **解决子问题**：
        a.  递归地计算左半部分的最大子数组和 (`maxLeftSum`)。
        b.  递归地计算右半部分的最大子数组和 (`maxRightSum`)。
        c.  计算跨越中点的最大子数组和 (`maxCrossingSum`)。
            * 这个跨越中点的子数组必然包含中点左侧的某个元素和中点右侧的某个元素。
            * 从中点向左扫描，找到以中点为右边界的最大和 (`leftBorderSum`)。
            * 从中点+1向右扫描，找到以中点+1为左边界的最大和 (`rightBorderSum`)。
            * `maxCrossingSum = leftBorderSum + rightBorderSum`。
    4.  **合并**：最终的最大子数组和是 `max(maxLeftSum, maxRightSum, maxCrossingSum)`。

* **Go语言实现 (分治法)**：
    ```go
    package main

    import (
        "fmt"
        "math"
    )

    func maxSubArray_divideConquer(nums []int) int {
        if len(nums) == 0 {
            return 0
        }
        return findMaxSubArray(nums, 0, len(nums)-1)
    }

    func findMaxSubArray(nums []int, left int, right int) int {
        // 基本情况：子数组只有一个元素
        if left == right {
            return nums[left]
        }

        mid := left + (right-left)/2

        // 1. 最大子数组和在左半部分
        maxLeftSum := findMaxSubArray(nums, left, mid)
        // 2. 最大子数组和在右半部分
        maxRightSum := findMaxSubArray(nums, mid+1, right)
        // 3. 最大子数组和跨越中点
        maxCrossingSum := findMaxCrossingSubArray(nums, left, mid, right)

        if maxLeftSum >= maxRightSum && maxLeftSum >= maxCrossingSum {
            return maxLeftSum
        } else if maxRightSum >= maxLeftSum && maxRightSum >= maxCrossingSum {
            return maxRightSum
        } else {
            return maxCrossingSum
        }
    }

    func findMaxCrossingSubArray(nums []int, left int, mid int, right int) int {
        // 计算包含中点左侧元素的最大和 (从 mid 向左扫描)
        leftSum := math.MinInt32
        currentSum := 0
        for i := mid; i >= left; i-- {
            currentSum += nums[i]
            if currentSum > leftSum {
                leftSum = currentSum
            }
        }

        // 计算包含中点右侧元素的最大和 (从 mid+1 向右扫描)
        rightSum := math.MinInt32
        currentSum = 0
        for i := mid + 1; i <= right; i++ {
            currentSum += nums[i]
            if currentSum > rightSum {
                rightSum = currentSum
            }
        }
        // 如果左右两边都只有负数，而题目允许子数组只包含一个元素，
        // 那么 cross sum 可能是 leftSum (如果 rightSum 很小) 或 rightSum (如果 leftSum 很小)
        // 或者 leftSum + rightSum.
        // 题目保证子数组至少一个元素，所以 leftSum 和 rightSum 不会是 MinInt32 如果nums[mid]或nums[mid+1]存在
        // 实际上，跨越中点的子数组必须包含 nums[mid] 和 nums[mid+1] (如果 mid+1 <= right)。
        // 但这里 `leftSum` 是以`nums[mid]`结尾的向左延伸的最大和，`rightSum` 是以`nums[mid+1]`开始向右延伸的最大和。
        // 所以它们的和 `leftSum + rightSum` 就是跨中点的最大和。

        return leftSum + rightSum
    }


    func main() {
        fmt.Println(maxSubArray_divideConquer([]int{-2, 1, -3, 4, -1, 2, 1, -5, 4})) // 6
        fmt.Println(maxSubArray_divideConquer([]int{1}))                             // 1
        fmt.Println(maxSubArray_divideConquer([]int{5, 4, -1, 7, 8}))                 // 23
        fmt.Println(maxSubArray_divideConquer([]int{-1}))                            // -1
        fmt.Println(maxSubArray_divideConquer([]int{-2, -1}))                        // -1
    }

    ```
    *修正 `findMaxCrossingSubArray`: `leftSum` 和 `rightSum` 必须包含元素，所以它们的初始值应该基于 `nums[mid]` 和 `nums[mid+1]` 或者从0开始累加。实际上，这里的 `math.MinInt32` 是为了确保任何第一个和都会比它大。`leftSum` 最终会是包含 `nums[mid]` 的最大向左延伸和，`rightSum` 会是包含 `nums[mid+1]` 的最大向右延伸和。所以 `leftSum + rightSum` 是正确的。*

* **复杂度分析 (分治法)**：
    * 时间复杂度：$T(N) = 2T(N/2) + O(N)$。根据主定理，这解出来是 $O(N \log N)$。其中 $O(N)$ 是 `findMaxCrossingSubArray` 的扫描时间。
    * 空间复杂度：$O(\log N)$，主要来自递归调用的栈深度。

* **比较**：虽然分治法是解决此问题的另一种有效思路，但 Kadane 算法的 $O(N)$ 时间复杂度和 $O(1)$ 空间复杂度更为优秀。

### 6.3 总结题目对应的解题模式

* **动态规划 (Kadane's Algorithm)**:
    Kadane 算法是解决最大子数组和问题的经典动态规划方法。其核心思想是维护两个变量：
    1.  `currentMaxEndingHere`：以当前元素结尾的子数组的最大和。
    2.  `maxSoFar`：到目前为止遇到的全局最大子数组和。
    状态转移的关键在于，`currentMaxEndingHere` 要么是当前元素本身，要么是当前元素加上前一个 `currentMaxEndingHere`（如果后者为正）。
    这个模式适用于寻找序列中具有某种“最优累积特性”的连续子段。

* **分治法 (Divide and Conquer)**:
    虽然不是此问题最优解，但分治法提供了一种结构化的方法来分解问题。它将问题分解为更小的、独立的子问题，解决子问题，然后合并结果。对于最大子数组和问题，关键在于处理跨越中点的情况。

### 6.4 泛化类似的问题

**Kadane 算法的变种和应用：**

1.  **最大子矩阵和 (Maximum Submatrix Sum)**：在一个二维矩阵中找到和最大的子矩阵。可以将问题转化为多个一维最大子数组和问题。例如，固定上下边界，然后将这两行之间的每列元素求和，形成一个一维数组，再对这个一维数组应用 Kadane 算法。
2.  **环形数组的最大子数组和 (Maximum Sum Circular Subarray)**：数组是环形的，意味着子数组可以从末尾绕到开头。这可以分解为两种情况：
    * 最大子数组和不跨越环形边界（即普通 Kadane）。
    * 最大子数组和跨越环形边界。这等价于：数组总和 - 最小子数组和 (最小子数组和可以通过将所有元素取反后跑 Kadane 算法得到)。
    需要注意全负数的情况。
3.  **只出现一次的数字 III (Single Number III)**：虽然不是直接用 Kadane，但 Kadane 的思想，即“如果累积效应变差，就重新开始”，在某些优化思路中可能有所体现。
4.  **乘积最大子数组 (Maximum Product Subarray)**：与和类似，但乘积需要考虑负数的情况（两个负数相乘为正）。因此需要同时维护当前最大乘积和当前最小乘积（因为最小负数乘以另一个负数可能变成最大正数）。
5.  **Best Time to Buy and Sell Stock (买卖股票的最佳时机)**：前面讲过，其 $O(N)$ 解法中维护 `minPrice` 和 `maxProfit` 的思想，与 Kadane 中维护局部最优和全局最优有相似之处。可以看作是差分数组的 Kadane。如果将 `prices` 数组转换为 `diff[i] = prices[i] - prices[i-1]` 的差分数组，那么原问题就变成了在差分数组中找最大子数组和。

**分治法的应用：**

1.  **归并排序 (Merge Sort)**：经典的 $O(N \log N)$ 排序算法。
2.  **快速排序 (Quick Sort)**：平均 $O(N \log N)$ 排序算法。
3.  **最近点对问题 (Closest Pair of Points)**：在平面上找到距离最近的两个点。
4.  **大整数乘法 (Karatsuba Algorithm)**：比传统方法更快的整数乘法。
5.  **快速傅里叶变换 (FFT)**：高效计算离散傅里叶变换。

Kadane 算法因其简洁和高效，在处理一维序列的连续子段最优化问题时非常强大。

---
下一个是 **Product of Array Except Self**。

## 7. Product of Array Except Self (除自身以外数组的乘积)

### 7.1 理解题目

* **题目描述**：给你一个整数数组 `nums`，返回一个数组 `answer`，其中 `answer[i]` 等于 `nums` 中除 `nums[i]` 之外其余各元素的乘积。
* **约束**：
    * 题目数据保证数组中任何元素的前缀或后缀的乘积都在 32-bit 整数范围内。
    * 请不要使用除法操作。
    * 在 $O(N)$ 时间复杂度内完成此题。
    * (进阶)：你可以在 $O(1)$ 的额外空间复杂度内完成吗？ (输出数组不被视为额外空间)。
* **主要考察点**：数组遍历、前缀积和后缀积。
* **目的**：在不使用除法且满足特定时空复杂度要求的情况下，计算数组元素的部分乘积。

### 7.2 从思路引出最优解决方案 (Go语言实现)

#### 思路一：使用除法 (不符合题目要求，但作为对比)

如果允许使用除法，思路很简单：
1.  计算整个数组所有元素的总乘积 `totalProduct`。
2.  遍历数组，对于每个 `nums[i]`，`answer[i] = totalProduct / nums[i]`。
3.  需要特殊处理 `nums[i]` 为 0 的情况：
    * 如果数组中有多个 0，则 `answer` 数组所有元素都为 0。
    * 如果数组中只有一个 0，设其位置为 `k`，则 `answer[k]` 等于其他所有非零元素的乘积，而其他 `answer[j]` (j != k) 都为 0。
    * 如果数组中没有 0，则按步骤 2 计算。

这个思路不满足“不使用除法”的要求，且 $O(N)$ 复杂度下处理 0 的情况也需要仔细。

#### 思路二：左右乘积列表 (Prefix and Suffix Products)

对于 `answer[i]`，它等于 `nums[i]` 左边所有元素的乘积乘以 `nums[i]` 右边所有元素的乘积。
`answer[i] = (nums[0] * ... * nums[i-1]) * (nums[i+1] * ... * nums[n-1])`

* **步骤**：
    1.  创建两个数组 `leftProducts` 和 `rightProducts`，长度都为 `n`。
    2.  `leftProducts[i]` 存储 `nums[0] * ... * nums[i-1]` 的乘积。
        * `leftProducts[0]` 可以设为 1 (因为 `nums[0]` 左边没有元素)。
        * 从 `i = 1` 到 `n-1`，计算 `leftProducts[i] = leftProducts[i-1] * nums[i-1]`。
    3.  `rightProducts[i]` 存储 `nums[i+1] * ... * nums[n-1]` 的乘积。
        * `rightProducts[n-1]` 可以设为 1 (因为 `nums[n-1]` 右边没有元素)。
        * 从 `i = n-2` 到 `0`（反向遍历），计算 `rightProducts[i] = rightProducts[i+1] * nums[i+1]`。
    4.  创建结果数组 `answer`。
    5.  遍历 `nums` 数组，对于每个 `i`，`answer[i] = leftProducts[i] * rightProducts[i]`。

* **Go语言实现**：
    ```go
    package main

    import "fmt"

    func productExceptSelf_leftRightArrays(nums []int) []int {
        n := len(nums)
        if n == 0 {
            return []int{}
        }

        leftProducts := make([]int, n)
        rightProducts := make([]int, n)
        answer := make([]int, n)

        // 计算 leftProducts
        leftProducts[0] = 1
        for i := 1; i < n; i++ {
            leftProducts[i] = leftProducts[i-1] * nums[i-1]
        }

        // 计算 rightProducts
        rightProducts[n-1] = 1
        for i := n - 2; i >= 0; i-- {
            rightProducts[i] = rightProducts[i+1] * nums[i+1]
        }

        // 计算最终结果
        for i := 0; i < n; i++ {
            answer[i] = leftProducts[i] * rightProducts[i]
        }

        return answer
    }

    func main() {
        fmt.Println(productExceptSelf_leftRightArrays([]int{1, 2, 3, 4})) // [24, 12, 8, 6]
        fmt.Println(productExceptSelf_leftRightArrays([]int{-1, 1, 0, -3, 3})) // [0, 0, 9, 0, 0]
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N)$。我们进行了三次遍历，每次都是 $O(N)$。
    * 空间复杂度：$O(N)$。我们使用了 `leftProducts`、`rightProducts` 和 `answer` 三个数组。如果 `answer` 数组不计入额外空间，那么额外空间是 $O(N)$ (用于 `leftProducts` 和 `rightProducts`)。

* **优点**：满足不使用除法和 $O(N)$ 时间复杂度的要求。

#### 思路三：空间优化 (O(1) Extra Space)

我们可以优化空间复杂度，使得除了输出数组 `answer` 外，只使用常数级别的额外空间。
这个思路是复用 `answer` 数组，并用一个变量来动态计算后缀积。

* **步骤**：
    1.  初始化 `answer` 数组，长度为 `n`。
    2.  **第一遍遍历（计算前缀积并存入 `answer`）**:
        * `answer[0]` 设为 1。
        * 从 `i = 1` 到 `n-1`，计算 `answer[i] = answer[i-1] * nums[i-1]`。
        * 此时，`answer[i]` 存储的是 `nums[i]` 左侧所有元素的乘积。
    3.  **第二遍遍历（从右向左，计算后缀积并乘以已存入的前缀积）**:
        * 引入一个变量 `rightProduct`，初始化为 1。这个变量将动态表示当前元素右侧所有元素的乘积。
        * 从 `i = n-1` 到 `0`（反向遍历）：
            a.  `answer[i] = answer[i] * rightProduct` (当前的 `answer[i]` 是左缀积，乘以 `rightProduct` 即为最终结果)。
            b.  更新 `rightProduct` 以备下一轮使用：`rightProduct = rightProduct * nums[i]`。
    4.  返回 `answer` 数组。

* **Go语言实现**：
    ```go
    package main

    import "fmt"

    func productExceptSelf_optimizedSpace(nums []int) []int {
        n := len(nums)
        if n == 0 {
            return []int{}
        }

        answer := make([]int, n)

        // 第一遍：计算前缀积，存储在 answer 数组中
        // answer[i] 将包含 nums[0]...nums[i-1] 的乘积
        answer[0] = 1
        for i := 1; i < n; i++ {
            answer[i] = answer[i-1] * nums[i-1]
        }

        // 第二遍：从右向左，累乘后缀积
        rightProduct := 1 // 用于存储右侧元素的累积乘积
        for i := n - 1; i >= 0; i-- {
            // 对于 answer[i]，它已经包含了其左侧所有元素的乘积
            //现在需要乘以其右侧所有元素的乘积 (rightProduct)
            answer[i] = answer[i] * rightProduct
            // 更新 rightProduct，为下一个左边的元素准备其右侧的乘积
            rightProduct = rightProduct * nums[i]
        }

        return answer
    }

    func main() {
        fmt.Println(productExceptSelf_optimizedSpace([]int{1, 2, 3, 4}))       // [24, 12, 8, 6]
        fmt.Println(productExceptSelf_optimizedSpace([]int{-1, 1, 0, -3, 3})) // [0, 0, 9, 0, 0]
        fmt.Println(productExceptSelf_optimizedSpace([]int{0,0})) // [0,0]
        fmt.Println(productExceptSelf_optimizedSpace([]int{0,4,0})) // [0,0,0]
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N)$。我们进行了两次遍历，每次都是 $O(N)$。
    * 空间复杂度：$O(1)$ (如果输出数组 `answer` 不计入额外空间)。我们只使用了一个额外的变量 `rightProduct`。

* **优点**：满足所有题目要求，包括 $O(1)$ 额外空间复杂度。

### 7.3 总结题目对应的解题模式

* **前缀/后缀计算 (Prefix/Suffix Computation)**:
    许多数组问题可以通过预计算前缀和、前缀积、后缀和、后缀积等信息来优化。对于本题，`answer[i]` 可以看作是 `nums[i]` 左边所有元素的“前缀积”与 `nums[i]` 右边所有元素的“后缀积”的乘积。
    通过两次遍历：一次从左到右计算前缀信息，一次从右到左计算后缀信息，然后合并，或者像优化解法那样，在一次遍历中计算一类信息，在另一次遍历中结合另一类信息和之前的结果。

### 7.4 泛化类似的问题

1.  **左右元素的最大值/最小值差**: 给定数组，对每个元素 `arr[i]`，找出 `max(arr[0...i-1])` 和 `min(arr[i+1...n-1])`，或者类似组合。可以预计算前缀最大/小值数组和后缀最大/小值数组。
2.  **接雨水 (Trapping Rain Water)**：对于每个位置 `i`，能接的雨水量取决于其左边的最高柱子 `leftMax[i]` 和右边的最高柱子 `rightMax[i]`。即 `min(leftMax[i], rightMax[i]) - height[i]`。需要预计算每个位置的左侧最高和右侧最高。
3.  **蜡烛之间的盘子 (Plates Between Candles)**：涉及到查询某个区间内特定元素的数量，可以预计算前缀和以及每个位置左右最近的特定元素的位置。
4.  **处理需要“左边所有...”和“右边所有...”信息的问题**: 任何需要对一个元素的左右两边子数组进行聚合计算的问题，都可以考虑前缀/后缀计算的模式。
5.  **Best Time to Buy and Sell Stock III/IV**：这些更复杂的股票问题中，动态规划的状态可能涉及到“从左到右”的最大利润和“从右到左”的最大利润（即如果时间倒流，从未来某点买入，过去某点卖出）。

这种模式的关键在于将对一个元素的计算分解为其左侧聚合值和右侧聚合值的某种组合，并通过高效的遍历来得到这些聚合值。

---
下一个是 **3Sum**。

## 8. 3Sum (三数之和)

### 8.1 理解题目

* **题目描述**：给你一个包含 `n` 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 `a`，`b`，`c` ，使得 `a + b + c = 0` ？请你找出所有和为 0 且不重复的三元组。
* **注意**：答案中不可以包含重复的三元组。例如，`[-1, 0, 1]` 和 `[0, -1, 1]` 被认为是同一个三元组。
* **主要考察点**：数组遍历、排序、双指针法、去重处理。
* **目的**：在数组中找到所有满足特定和（这里是0）的三个不同元素的组合，并处理结果的唯一性。

### 8.2 从思路引出最优解决方案 (Go语言实现)

#### 思路一：暴力枚举 (Brute Force)

最直接的方法是尝试所有可能的三元组。

* **步骤**：
    1.  使用三层嵌套循环：
        * 外层循环 `i` 从 `0` 到 `n-3`。
        * 中层循环 `j` 从 `i+1` 到 `n-2`。
        * 内层循环 `k` 从 `j+1` 到 `n-1`。
    2.  检查 `nums[i] + nums[j] + nums[k] == 0`。
    3.  如果满足条件，将 `[nums[i], nums[j], nums[k]]` 添加到结果列表中。
    4.  **去重**：由于答案不能包含重复的三元组，一个简单的方法是将找到的三元组排序后存入一个哈希集合（例如，将排序后的三元组转换为字符串作为键），或者在添加到最终结果列表前检查是否已存在（排序后比较）。

* **Go语言实现 (带排序去重)**：
    ```go
    package main

    import (
        "fmt"
        "sort"
        "strconv"
        "strings"
    )

    func threeSum_bruteForce(nums []int) [][]int {
        n := len(nums)
        if n < 3 {
            return [][]int{}
        }

        resultSet := make(map[string][]int) // 使用 map 去重

        for i := 0; i < n-2; i++ {
            for j := i + 1; j < n-1; j++ {
                for k := j + 1; k < n; k++ {
                    if nums[i]+nums[j]+nums[k] == 0 {
                        triplet := []int{nums[i], nums[j], nums[k]}
                        sort.Ints(triplet) // 排序以保证唯一性
                        key := fmt.Sprintf("%d,%d,%d", triplet[0], triplet[1], triplet[2])
                        resultSet[key] = triplet
                    }
                }
            }
        }

        result := make([][]int, 0, len(resultSet))
        for _, v := range resultSet {
            result = append(result, v)
        }
        return result
    }

    func main() {
        fmt.Println(threeSum_bruteForce([]int{-1, 0, 1, 2, -1, -4}))
        // Expected: [[-1, -1, 2], [-1, 0, 1]] (order might vary)
        fmt.Println(threeSum_bruteForce([]int{0,1,1})) // []
        fmt.Println(threeSum_bruteForce([]int{0,0,0})) // [[0,0,0]]
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N^3)$ 用于三层循环。排序三元组是 $O(3 \log 3) = O(1)$。哈希表操作平均 $O(1)$（假设键长度固定）。所以总体是 $O(N^3)$。
    * 空间复杂度：$O(M)$，其中 $M$ 是找到的不重复三元组的数量，用于存储结果和哈希集合。最坏情况下 $M$ 可能是 $O(N^3)$ 级别（虽然对于三数之和为0，实际上三元组数量不会那么多），但通常是 $O(N^2)$ 或更少。如果只考虑辅助空间，哈希集合的大小是关键。

* **缺点**：$O(N^3)$ 时间复杂度太高，对于典型的 LeetCode 约束 (如 N=3000) 会超时。

#### 思路二：排序 + 双指针 (Sort + Two Pointers)

这是解决这类 K-Sum 问题的经典优化方法。通过先对数组排序，我们可以利用双指针技术在 $O(N)$ 的时间内查找两个数的和。

* **步骤**：
    1.  **排序**：首先对整个数组 `nums` 进行升序排序。时间复杂度 $O(N \log N)$。
    2.  **主循环 (固定第一个数)**：遍历排序后的数组，对于每个元素 `nums[i]` (作为三元组中的第一个数 `a`)：
        a.  **去重 `a`**：如果 `i > 0` 且 `nums[i] == nums[i-1]`，则说明这个 `nums[i]` 作为第一个数的情况已经被考虑过了（因为排序后相同的元素会相邻），跳过当前 `nums[i]` 以避免重复的三元组。
        b.  **目标值转换**：我们需要找到另外两个数 `nums[left]` 和 `nums[right]`，使得 `nums[left] + nums[right] == -nums[i]`。令 `targetTwoSum = -nums[i]`。
        c.  **双指针查找**：
            i.  初始化左指针 `left = i + 1` 和右指针 `right = n - 1` (n 是数组长度)。
            ii. 当 `left < right` 时，进行循环：
                * 计算 `currentSum = nums[left] + nums[right]`。
                * **如果 `currentSum == targetTwoSum`**：
                    * 找到了一个三元组 `[nums[i], nums[left], nums[right]]`。将其添加到结果列表中。
                    * **去重 `b` 和 `c`**：为了避免因 `nums[left]` 或 `nums[right]` 重复而导致的三元组重复：
                        * 向右移动 `left` 指针，跳过所有与当前 `nums[left]` 相同的元素：`while left < right && nums[left] == nums[left+1] { left++ }`。
                        * 向左移动 `right` 指针，跳过所有与当前 `nums[right]` 相同的元素：`while left < right && nums[right] == nums[right-1] { right-- }`。
                        * 然后，将 `left` 右移一位，`right` 左移一位，继续寻找其他可能的 `b` 和 `c`。 `left++; right--`。
                * **如果 `currentSum < targetTwoSum`**：说明和太小了，需要增大和。由于数组已排序，将左指针 `left` 右移一位 (`left++`)，尝试一个更大的数。
                * **如果 `currentSum > targetTwoSum`**：说明和太大了，需要减小和。由于数组已排序，将右指针 `right` 左移一位 (`right--`)，尝试一个更小的数。
    3.  返回结果列表。

* **Go语言实现**：
    ```go
    package main

    import (
        "fmt"
        "sort"
    )

    func threeSum_sortTwoPointers(nums []int) [][]int {
        n := len(nums)
        if n < 3 {
            return [][]int{}
        }

        sort.Ints(nums) // 排序是关键
        result := [][]int{}

        for i := 0; i < n-2; i++ {
            // 去重：如果当前数字和前一个数字相同，则跳过
            // 因为以 nums[i-1] 为第一个数的情况已经包含了所有可能性
            if i > 0 && nums[i] == nums[i-1] {
                continue
            }

            // 优化：如果 nums[i] > 0，由于数组已排序，
            // nums[left] 和 nums[right] 也必然 >= nums[i] > 0，
            // 它们的和不可能为 0。所以可以直接中断循环。
            // (注意：如果目标不是0，这个优化不一定适用)
            // 对于本题目标是0，如果 nums[i] > 0, 且 nums[left] >= nums[i], nums[right] >= nums[i]
            // 那么 nums[i] + nums[left] + nums[right] > 0. 所以可以 break.
            if nums[i] > 0 { 
                break
            }


            left, right := i+1, n-1
            targetTwoSum := -nums[i]

            for left < right {
                currentSum := nums[left] + nums[right]

                if currentSum == targetTwoSum {
                    result = append(result, []int{nums[i], nums[left], nums[right]})

                    // 去重：跳过重复的 nums[left]
                    for left < right && nums[left] == nums[left+1] {
                        left++
                    }
                    // 去重：跳过重复的 nums[right]
                    for left < right && nums[right] == nums[right-1] {
                        right--
                    }
                    // 移动指针到下一个不同的元素
                    left++
                    right--
                } else if currentSum < targetTwoSum {
                    left++
                } else { // currentSum > targetTwoSum
                    right--
                }
            }
        }
        return result
    }

    func main() {
        fmt.Println(threeSum_sortTwoPointers([]int{-1, 0, 1, 2, -1, -4}))
        // Output: [[-1, -1, 2], [-1, 0, 1]] (order of triplets might vary, elements within triplet are sorted by the append)
        // My sort.Ints(nums) will sort it to: [-4, -1, -1, 0, 1, 2]
        // i=0, nums[i]=-4, targetTwoSum=4. left=1(-1), right=5(2). sum=1 < 4. left++
        // left=2(-1), right=5(2). sum=1 < 4. left++
        // left=3(0), right=5(2). sum=2 < 4. left++
        // left=4(1), right=5(2). sum=3 < 4. left++ (left becomes 5, loop ends)
        // i=1, nums[i]=-1, targetTwoSum=1. left=2(-1), right=5(2). sum=1 == 1. Add [-1,-1,2].
        //      skip left: nums[2]==nums[3]? -1 != 0. No.
        //      skip right: nums[5]==nums[4]? 2 != 1. No.
        //      left=3(0), right=4(1). sum=1 == 1. Add [-1,0,1].
        //      skip left, skip right. left=4, right=3. loop ends.
        // i=2, nums[i]=-1. nums[2]==nums[1], continue. (This is the first number skip)
        // i=3, nums[i]=0, targetTwoSum=0. left=4(1), right=5(2). sum=3 > 0. right--
        // left=4(1), right=4(1). loop ends.
        // i=4, nums[i]=1. nums[i]>0, break.

        fmt.Println(threeSum_sortTwoPointers([]int{0,1,1})) // []
        fmt.Println(threeSum_sortTwoPointers([]int{0,0,0})) // [[0,0,0]]
        fmt.Println(threeSum_sortTwoPointers([]int{0,0,0,0})) // [[0,0,0]]
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N^2)$。排序需要 $O(N \log N)$。外层循环 `i` 遍历 $N$ 个元素。对于每个 `i`，双指针 `left` 和 `right` 的扫描是 $O(N)$ 的。所以总体是 $O(N \log N + N^2) = O(N^2)$。
    * 空间复杂度：$O(\log N)$ 或 $O(N)$，取决于排序算法使用的空间（Go 的 `sort.Ints` 是原地排序，但可能使用 $O(\log N)$ 的栈空间）。结果列表的空间不算在额外空间内，如果算的话是 $O(M)$，其中 $M$ 是三元组数量。如果不允许修改原数组而需要拷贝，则为 $O(N)$。通常我们认为排序在原数组上进行，所以额外空间为 $O(\log N)$ (递归栈) 或 $O(1)$ (迭代式排序)。

* **优点**：显著降低了时间复杂度，是解决此问题的标准高效方案。去重逻辑也比较自然地融入了双指针移动的过程中。

### 8.3 总结题目对应的解题模式

* **排序 + 双指针 (Sort + Two Pointers)**:
    这是解决 K-Sum 类型问题（寻找 K 个数之和为目标值）以及其他需要在有序数组中查找满足特定条件的数对或多元组的常用模式。
    1.  **排序**是前提，它使得我们可以通过移动指针来增大或减小当前考虑的元素的和。
    2.  **固定一个或多个元素** (对于 3Sum，固定一个元素 `nums[i]`)。
    3.  **将问题转化为一个更小规模的问题** (对于 3Sum，转化为在剩余部分查找两数之和为 `target - nums[i]` 的 Two Sum 问题)。
    4.  **使用双指针** (一个从头开始，一个从尾开始) 在排序后的剩余部分高效地查找这两个数。
    5.  **处理重复**：在排序后，重复元素会相邻，这使得去重逻辑更容易实现，通常是在移动指针时跳过与前一个已处理元素相同的元素。

### 8.4 泛化类似的问题

1.  **Two Sum (两数之和)**：
    * 如果输入数组已排序 (Two Sum II - Input array is sorted)，可以直接用双指针法在 $O(N)$ 时间，$O(1)$ 空间解决。
    * 如果未排序，哈希表法 $O(N)$ 时间，$O(N)$ 空间；或者先排序再双指针 $O(N \log N)$ 时间，$O(1)$ 或 $O(\log N)$ 空间。
2.  **4Sum (四数之和)**：可以先排序，然后固定两个数 `nums[i]` 和 `nums[j]`，再在剩余部分用双指针法查找另外两个数，总时间复杂度 $O(N^3)$。同样需要仔细处理去重。
3.  **K-Sum (K数之和)**：可以推广，通过递归或多层循环固定 `k-2` 个数，然后用双指针解决剩下的 Two Sum 问题。
4.  **最接近的三数之和 (3Sum Closest)**：与 3Sum 非常相似，只是目标是找到和最接近 `target` 的三元组，而不是正好等于 `target`。双指针逻辑中更新“最小差值”和对应的和。
5.  **盛最多水的容器 (Container With Most Water)**：虽然不是 K-Sum，但它也是一个在数组上使用双指针（从两端向中间移动）来优化搜索空间的经典问题。
6.  **有效三角形的个数 (Valid Triangle Number)**：给定一个数组，找出可以组成三角形三边的三个数的组合数量。排序后，固定两条较短的边，然后用二分查找或双指针找到第三条边的可能范围。或者固定最长边 `c`，然后用双指针在 `c` 左边的部分找 `a` 和 `b` 使得 `a+b > c`。

排序 + 双指针模式对于处理有序数据中的查找和组合问题非常强大和高效。

---
下一个是 **Merge Intervals**。

## 9. Merge Intervals (合并区间)

### 9.1 理解题目

* **题目描述**：给出一个区间的集合 `intervals`，其中 `intervals[i] = [start_i, end_i]`。请你合并所有重叠的区间，并返回一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。
* **区间重叠的定义**：例如 `[1,3]` 和 `[2,6]` 重叠，合并后为 `[1,6]`。`[1,4]` 和 `[4,5]` 也被认为是重叠的（或可连接的），合并为 `[1,5]`。
* **主要考察点**：排序、区间操作、贪心算法。
* **目的**：将一组可能重叠的区间化简为最少数量的不重叠区间，同时保持覆盖范围不变。

### 9.2 从思路引出最优解决方案 (Go语言实现)

#### 思路：排序 + 逐个合并 (Sort + Greedy Merge)

如果区间是无序的，很难判断它们是否重叠以及如何合并。一个关键的洞察是，如果我们将区间**按起始点排序**，那么处理起来会变得简单得多。

* **步骤**：
    1.  **排序**：将区间数组 `intervals` 按照每个区间的起始点 `start_i` 升序排序。如果起始点相同，可以按结束点排序，但这通常不是必需的，因为后续逻辑会处理。时间复杂度 $O(N \log N)$。
    2.  **初始化结果列表**：创建一个空的结果列表 `mergedIntervals`。将排序后的第一个区间加入到 `mergedIntervals` 中。
    3.  **遍历并合并**：从排序后的第二个区间开始，遍历剩余的区间 `currentInterval`：
        a.  获取 `mergedIntervals` 中的最后一个区间 `lastMergedInterval`。
        b.  **判断重叠**：
            * 如果 `currentInterval.start <= lastMergedInterval.end` (即当前区间的开始点小于或等于最后一个已合并区间的结束点)，说明它们重叠或者可以连接。
            * **合并操作**：更新 `lastMergedInterval.end = max(lastMergedInterval.end, currentInterval.end)`。这意味着将当前区间合并到最后一个已合并区间中，并扩展其结束点以包含当前区间。
        c.  **不重叠**：
            * 如果 `currentInterval.start > lastMergedInterval.end`，说明当前区间与最后一个已合并区间不重叠。
            * **添加新区间**：将 `currentInterval` 作为一个新的独立区间添加到 `mergedIntervals` 列表中。
    4.  返回 `mergedIntervals`。

* **Go语言实现**：
    ```go
    package main

    import (
        "fmt"
        "sort"
    )

    func mergeIntervals(intervals [][]int) [][]int {
        if len(intervals) == 0 {
            return [][]int{}
        }

        // 1. 按起始点对区间进行排序
        sort.Slice(intervals, func(i, j int) bool {
            return intervals[i][0] < intervals[j][0]
        })

        mergedIntervals := [][]int{}
        // 2. 将第一个区间加入结果列表 (或在循环开始前处理)
        mergedIntervals = append(mergedIntervals, intervals[0])

        // 3. 遍历并合并
        for i := 1; i < len(intervals); i++ {
            currentInterval := intervals[i]
            // 获取 mergedIntervals 中的最后一个区间
            // 注意：这里不能直接用 lastMergedInterval := mergedIntervals[len(mergedIntervals)-1]
            // 因为切片中的元素是值拷贝，修改 lastMergedInterval 不会修改 mergedIntervals 中的原值。
            // 所以需要通过索引访问和修改：
            lastMergedIndex := len(mergedIntervals) - 1

            // 判断重叠
            if currentInterval[0] <= mergedIntervals[lastMergedIndex][1] {
                // 重叠，合并。更新最后一个已合并区间的结束点
                if currentInterval[1] > mergedIntervals[lastMergedIndex][1] {
                     mergedIntervals[lastMergedIndex][1] = currentInterval[1]
                }
                // mergedIntervals[lastMergedIndex][1] = max(mergedIntervals[lastMergedIndex][1], currentInterval[1])
            } else {
                // 不重叠，将当前区间作为新区间添加到结果列表
                mergedIntervals = append(mergedIntervals, currentInterval)
            }
        }

        return mergedIntervals
    }
    
    // Helper for max if not directly using math.Max with float conversion
    // func max(a, b int) int {
    //  if a > b {
    //      return a
    //  }
    //  return b
    // }

    func main() {
        fmt.Println(mergeIntervals([][]int{{1, 3}, {2, 6}, {8, 10}, {15, 18}}))
        // Expected: [[1,6],[8,10],[15,18]]
        // Sorted: [[1,3], [2,6], [8,10], [15,18]]
        // merged: [[1,3]]
        // current: [2,6]. 2 <= 3. merged[0][1] = max(3,6) = 6. merged: [[1,6]]
        // current: [8,10]. 8 > 6. merged: [[1,6], [8,10]]
        // current: [15,18]. 15 > 10. merged: [[1,6], [8,10], [15,18]]

        fmt.Println(mergeIntervals([][]int{{1, 4}, {4, 5}}))
        // Expected: [[1,5]]
        // Sorted: [[1,4], [4,5]]
        // merged: [[1,4]]
        // current: [4,5]. 4 <= 4. merged[0][1] = max(4,5) = 5. merged: [[1,5]]
        
        fmt.Println(mergeIntervals([][]int{{1,4},{0,4}}))
        // Expected: [[0,4]]
        // Sorted: [[0,4], [1,4]]
        // merged: [[0,4]]
        // current: [1,4]. 1 <= 4. merged[0][1] = max(4,4) = 4. merged: [[0,4]]

        fmt.Println(mergeIntervals([][]int{{1,4},{0,0}}))
        // Expected: [[0,0],[1,4]]
        // Sorted: [[0,0], [1,4]]
        // merged: [[0,0]]
        // current: [1,4]. 1 > 0. merged: [[0,0], [1,4]]
        
        fmt.Println(mergeIntervals([][]int{{2,3},{4,5},{6,7},{8,9},{1,10}}))
        // Expected: [[1,10]]
        // Sorted: [[1,10],[2,3],[4,5],[6,7],[8,9]]
        // merged: [[1,10]]
        // current: [2,3]. 2<=10. merged[0][1] = max(10,3)=10. merged: [[1,10]]
        // current: [4,5]. 4<=10. merged[0][1] = max(10,5)=10. merged: [[1,10]]
        // ...
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(N \log N)$，瓶颈在于排序。遍历和合并的过程是 $O(N)$。
    * 空间复杂度：$O(N)$ 或 $O(\log N)$。排序可能需要 $O(N)$（如归并排序的某些实现）或 $O(\log N)$（如快速排序的递归栈空间，Go 的 `sort.Slice` 一般是 IntroSort，结合了快速排序、堆排序、插入排序，原地性较好，栈空间 $O(\log N)$）。结果列表 `mergedIntervals` 最坏情况下可能包含所有不重叠的原始区间，即 $O(N)$。如果允许修改原数组且排序为原地，则可以认为额外空间是 $O(\log N)$（排序栈）+ $O(N)$（结果，题目通常不把结果算额外空间）。

* **优点**：思路清晰，通过排序简化了问题，贪心策略有效。

### 9.3 总结题目对应的解题模式

* **排序 + 贪心 (Sort + Greedy for Intervals)**:
    这是处理区间问题的常用模式。
    1.  **排序**：通常按区间的起始点（有时也按结束点，或组合）进行排序。这是贪心策略能够正确应用的前提。
    2.  **贪心选择**：在遍历排序后的区间时，基于局部最优选择来构建全局最优解。对于合并区间，贪心选择是：如果当前区间与已合并的最后一个区间重叠，则将它们合并；否则，当前区间开始一个新的合并区间。
    这个模式的关键在于，排序后，我们只需要考虑当前区间与“最近”的已处理（或正在形成）的区间之间的关系。

### 9.4 泛化类似的问题

1.  **插入区间 (Insert Interval)**：给定一组不重叠的区间和一个新区间，将新区间插入到原有区间中，如果产生重叠则合并。可以先找到新区间应该插入的位置，然后合并重叠部分。或者，将新区间加入列表，然后整体跑一遍合并区间算法。
2.  **无重叠区间 (Non-overlapping Intervals)**：给定一个区间的集合，找到需要移除区间的最小数量，使剩余区间互不重叠。通常按结束点排序，然后贪心地选择尽可能多的不重叠区间。
3.  **会议室 (Meeting Rooms)**：给定一系列会议的开始和结束时间，判断一个人是否能参加所有会议（即会议时间有无重叠）。排序后检查相邻会议是否有重叠。
4.  **会议室 II (Meeting Rooms II)**：给定一系列会议的开始和结束时间，计算最少需要多少间会议室才能安排所有会议。可以使用最小堆（优先队列）来管理会议的结束时间。按开始时间排序会议，遍历会议，如果当前会议的开始时间大于堆顶（最早结束）的会议的结束时间，则可以复用会议室（弹出堆顶，加入当前会议结束时间）；否则，需要新会议室（加入当前会议结束时间）。堆的大小即为所需会议室数量。这也是一种贪心。
5.  **用最少数量的箭引爆气球 (Minimum Number of Arrows to Burst Balloons)**：与无重叠区间类似，目标是用最少的箭射爆所有气球（每个气球代表一个x轴上的区间）。按结束点（或开始点）排序，然后贪心选择。
6.  **任务调度器 (Task Scheduler)** (某些变种可能涉及时间区间)：虽然核心是贪心和计数，但如果任务有执行时间窗口，也可能涉及区间处理。

区间问题通常都从排序入手，然后根据具体问题设计贪心策略或使用其他数据结构（如堆）来辅助决策。

---
最后一个是 **Group Anagrams**。

## 10. Group Anagrams (字母异位词分组)

### 10.1 理解题目

* **题目描述**：给你一个字符串数组 `strs`，请你将 **字母异位词** 组合在一起。你可以按任意顺序返回结果列表。
* **字母异位词**：在 "Valid Anagram" 中已解释，指由相同字母但顺序不同的字符串。
* **主要考察点**：哈希表、字符串处理、排序或字符计数。
* **目的**：根据特定属性（这里是字母组成相同）对一组对象（字符串）进行分组。

### 10.2 从思路引出最优解决方案 (Go语言实现)

核心思路是为每个字母异位词组找到一个唯一的“规范表示”或“签名”，然后使用这个签名作为哈希表的键来将具有相同签名的字符串分到同一组。

#### 思路一：排序作为签名 (Sorted String as Key)

如果两个字符串是字母异位词，那么它们排序后的形式是完全相同的。我们可以用排序后的字符串作为哈希表的键。

* **步骤**：
    1.  创建一个哈希表 `groupsMap`，其中键是排序后的字符串（签名），值是属于该签名的原始字符串列表（`[]string`）。
    2.  遍历输入的字符串数组 `strs` 中的每个字符串 `s`：
        a.  将字符串 `s` 转换为字符数组，对其进行排序，然后转换回字符串，得到签名 `signature`。
        b.  将原始字符串 `s` 添加到 `groupsMap[signature]` 对应的列表中。
    3.  遍历 `groupsMap` 中的所有值（即所有字符串列表），将它们收集到一个结果列表 `[][]string` 中。
    4.  返回结果列表。

* **Go语言实现**：
    ```go
    package main

    import (
        "fmt"
        "sort"
        "strings"
    )

    // Helper function from "Valid Anagram" to sort a string
    func sortStringForKey(s string) string {
        chars := strings.Split(s, "") // For general strings, rune slice might be better
        sort.Strings(chars)
        return strings.Join(chars, "")
    }

    func groupAnagrams_sortKey(strs []string) [][]string {
        if len(strs) == 0 {
            return [][]string{}
        }

        groupsMap := make(map[string][]string)

        for _, s := range strs {
            signature := sortStringForKey(s)
            groupsMap[signature] = append(groupsMap[signature], s)
        }

        result := make([][]string, 0, len(groupsMap))
        for _, group := range groupsMap {
            result = append(result, group)
        }
        return result
    }

    func main() {
        fmt.Println(groupAnagrams_sortKey([]string{"eat", "tea", "tan", "ate", "nat", "bat"}))
        // Expected: [["bat"],["nat","tan"],["ate","eat","tea"]] (order of groups and strings within groups can vary)
        // "eat" -> "aet" -> map["aet"] = ["eat"]
        // "tea" -> "aet" -> map["aet"] = ["eat", "tea"]
        // "tan" -> "ant" -> map["ant"] = ["tan"]
        // "ate" -> "aet" -> map["aet"] = ["eat", "tea", "ate"]
        // "nat" -> "ant" -> map["ant"] = ["tan", "nat"]
        // "bat" -> "abt" -> map["abt"] = ["bat"]

        fmt.Println(groupAnagrams_sortKey([]string{""})) // [[""]]
        fmt.Println(groupAnagrams_sortKey([]string{"a"})) // [["a"]]
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(M \cdot K \log K)$，其中 $M$ 是字符串的数量， $K$ 是字符串的最大长度。对于每个字符串，排序需要 $O(K \log K)$。哈希表操作平均 $O(K)$（因为键的比较和哈希计算可能涉及整个键的长度）。
    * 空间复杂度：$O(M \cdot K)$，用于存储哈希表。键和值列表中的字符串都需要空间。

* **优点**：思路直观，易于实现。

#### 思路二：字符计数作为签名 (Character Count as Key)

对于字母异位词，它们每个字符的出现次数是完全相同的。我们可以用一个表示字符计数的元组或字符串作为签名。例如，对于只包含小写字母的字符串，可以用一个长度为 26 的整数数组来表示计数，然后将这个数组转换为一个唯一的字符串键。

* **步骤**：
    1.  创建一个哈希表 `groupsMap`，键是字符计数签名（字符串形式），值是原始字符串列表。
    2.  遍历输入的字符串数组 `strs` 中的每个字符串 `s`：
        a.  创建一个大小为 26 的整数数组 `count` (假设只有小写字母)。
        b.  遍历字符串 `s`，统计每个字符的出现次数，更新 `count` 数组（`count[char - 'a']++`）。
        c.  将 `count` 数组转换为一个唯一的字符串形式作为签名 `signature`。例如，可以将 `[1,0,0,...,2]` 转换为 `"1#0#0#...#2"`。（选择一个不容易在数字中出现的分隔符）。
        d.  将原始字符串 `s` 添加到 `groupsMap[signature]` 对应的列表中。
    3.  收集 `groupsMap` 中的值到结果列表。
    4.  返回结果列表。

* **Go语言实现**：
    ```go
    package main

    import (
        "fmt"
        "strconv"
        "strings"
    )

    func groupAnagrams_countKey(strs []string) [][]string {
        if len(strs) == 0 {
            return [][]string{}
        }

        groupsMap := make(map[string][]string)

        for _, s := range strs {
            count := [26]int{} // Array for character counts ('a' to 'z')
            for _, char := range s {
                count[char-'a']++
            }

            // Convert count array to a string key
            // Example: [1,2,0...] -> "1#2#0..."
            var sb strings.Builder
            for i, c := range count {
                // To make it slightly more robust, could use a non-digit separator
                // and also handle the count for each char, even if 0.
                // Or, only append non-zero counts with their char: "a1b2"
                // For fixed-size array, simple concatenation with separator is fine.
                sb.WriteString(strconv.Itoa(c))
                if i < 25 { // Add separator for all but the last element
                    sb.WriteByte('#')
                }
            }
            signature := sb.String()

            groupsMap[signature] = append(groupsMap[signature], s)
        }

        result := make([][]string, 0, len(groupsMap))
        for _, group := range groupsMap {
            result = append(result, group)
        }
        return result
    }

    func main() {
        fmt.Println(groupAnagrams_countKey([]string{"eat", "tea", "tan", "ate", "nat", "bat"}))
        // "eat": count['e'-'a']++, count['a'-'a']++, count['t'-'a']++ -> e.g., count['a']=1, count['e']=1, count['t']=1
        // signature for "eat": "1#0#0#0#1#0#0#0#0#0#0#0#0#0#0#0#0#0#0#1#0#0#0#0#0#0"
        // "tea": same signature
        fmt.Println(groupAnagrams_countKey([]string{""}))
        fmt.Println(groupAnagrams_countKey([]string{"a"}))
    }
    ```

* **复杂度分析**：
    * 时间复杂度：$O(M \cdot K)$，其中 $M$ 是字符串的数量，$K$ 是字符串的最大长度。对于每个字符串：
        * 计算字符计数数组需要 $O(K)$。
        * 将计数数组转换为字符串键也需要 $O(K_c)$，其中 $K_c$ 是字符集大小（这里是26，常数），或者与 $K$ 相关如果生成的字符串键很长。如果键是固定长度的（如26个数字加分隔符），那么是 $O(1)$ 或 $O(\text{alphabet_size})$。
        * 哈希表操作平均 $O(K)$ (或 $O(\text{alphabet_size})$ for key ops)。
        所以总体是 $O(M \cdot K)$。
    * 空间复杂度：$O(M \cdot K)$，用于存储哈希表。键本身（计数串）的长度是固定的 (与字母表大小相关)，但值列表中的字符串总长度是 $M \cdot K$。

* **比较**：
    * 排序法 ($O(M \cdot K \log K)$) vs 计数法 ($O(M \cdot K)$)。
    * 当 $K$ 较大时，$\log K$ 会有影响，计数法理论上更快。
    * 当字符集非常大时（比如包含Unicode字符），固定大小的计数数组不再适用，需要用哈希表来计数，或者排序法可能更简单。对于本题限定的小写字母，计数法有优势。

### 10.3 总结题目对应的解题模式

* **规范表示/签名 + 哈希表分组 (Canonical Representation/Signature + Hash Map Grouping)**:
    当需要根据某种等价关系对对象进行分组时（例如，字母异位词是等价的），可以为每个等价类找到一个唯一的“规范表示”或“签名”。然后，使用这个签名作为哈希表的键，将所有具有相同签名的对象收集到对应的值（通常是一个列表）中。
    常见的签名方法：
    1.  **排序**：对于可排序的元素序列（如字符串中的字符、数字列表），排序后的形式可以作为签名。
    2.  **计数**：对于元素组成重要而顺序不重要的集合（如字母异位词），元素的频率分布（计数）可以作为签名。这通常转换为一个元组、固定大小的数组或其字符串表示。

### 10.4 泛化类似的问题

1.  **有效的字母异位词 (Valid Anagram)**：是此问题的基础，判断两个字符串是否是字母异位词，即它们是否有相同的签名。
2.  **子集 II (Subsets II)** / **排列 II (Permutations II)**：这些问题中包含重复元素，需要生成不重复的子集/排列。通常通过排序预处理，然后在递归生成时跳过重复分支，或者将生成的结果用规范形式存入哈希集合去重。
3.  **同构字符串 (Isomorphic Strings)**：判断两个字符串是否同构。需要建立字符间的一一映射关系。可以为每个字符串生成一个基于字符首次出现位置的模式签名，例如 "egg" -> "011", "add" -> "011"。
4.  **单词规律 (Word Pattern)**：判断一个模式 `pattern` 和一个字符串 `str` 是否遵循相同的规律。例如 `pattern = "abba"`, `str = "dog cat cat dog"`。与同构字符串类似，需要建立模式字符到单词的一一映射。
5.  **根据数字二进制下 1 的数目排序 (Sort Integers by The Number of 1 Bits)**：需要按自定义规则（1的位数，然后按数值大小）排序。可以看作是为每个数字计算一个“签名”（1的位数），然后基于这个签名进行分组或排序。
6.  **自定义排序和分组**：任何需要根据对象的某个派生属性进行分组或排序的问题，都可以抽象为：计算该属性（作为签名），然后使用哈希表分组或使用自定义比较函数排序。

这种模式非常通用，适用于许多需要识别和聚合等价对象的问题。

---

所有问题都已讲解完毕！希望这些详细的分析对你学习算法有所帮助。