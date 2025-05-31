---
layout: single
title: "zero-array-transformation-i(LeetCode)"
categories: CodingTest
tags: [prefix-sum, DP]
---

## <a href="https://leetcode.com/problems/zero-array-transformation-i/description/" target="_blank">zero-array-transformation-i</a>

```cpp
class Solution {
public:
    bool isZeroArray(vector<int>& nums, vector<vector<int>>& queries)
    {
        vector<int> dp(nums.size()+1, 0);
        for(auto& q : queries)
            ++dp[q[0]], --dp[q[1]+1];

        int sum = 0;
        for(int i=0; i<nums.size(); ++i)
        {
            sum += dp[i];
            if(nums[i] - sum > 0)
                return false;
        }
        return true;
    }
};
```

### 제한 사항

- 1 <= nums.length <= 10^5
- 1 <= queries.length <= 10^5

### 풀이

- 처음에 문제가 이해가 되지 않아 Copilot에게 설명을 요청
  - queries에는 인덱스의 범위가 주어진다.
  - nums 배열의 각 값에 대해 queries의 범위에 해당하는 인덱스의 값을 1씩 감소시킬수 있다.
  - 인덱스의 subset이므로 내 마음대로 인덱스를 선택할수 있다. 공집합도 가능하다.
  - 그런 식으로 모든 queries를 적용했을때 nums의 모든 값이 0이 될수 있는지 확인하는 문제이다.

- 단순하게 모든 쿼리를 적용해보면 O(n^2)으로 시간초과가 발생한다.
- dp를 이용해서 일단 범위의 start에는 +1, end+1에는 -1을 해준다.
- nums의 각 인덱스에 대해 dp의 누적합을 구해 nums[i] 값과 비교하면 된다.
- 인덱스는 내 마음대로 선택할수 있으므로 그 값이 0보다 크면 무조건 false이다.
