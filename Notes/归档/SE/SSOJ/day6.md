继续完善 dao 层与 mapper

晚上七点，完成所有 dao 层与 mapper 的定义，下一步是实现 service 层

ProblemService 示例：

```Java
package com.ssoj.backend.service;

import com.ssoj.backend.entity.Problem;
import com.ssoj.backend.dao.ProblemMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * 题目业务逻辑服务
 */
@Service
public class ProblemService {

    @Autowired
    private ProblemMapper problemMapper;

    /**
     * 获取题目列表（分页）
     */
    public List<Problem> getProblems(int page, int size) {
        // 参数校验
        if (page < 1) {
            throw new IllegalArgumentException("页码必须 >= 1");
        }
        if (size < 1 || size > 100) {
            throw new IllegalArgumentException("每页数量必须在 1-100 之间");
        }
        int offset = (page - 1) * size;
        return problemMapper.findAll(offset, size);
    }

    /**
     * 根据ID获取题目详情
     */
    public Problem getProblemById(Long id) {
        if (id == null || id <= 0) {
            throw new IllegalArgumentException("题目ID无效");
        }
        Problem problem = problemMapper.findById(id);
        if (problem == null) {
            throw new RuntimeException("题目不存在: " + id);
        }
        return problem;
    }

    /**
     * 根据难度筛选题目
     */
    public List<Problem> getProblemsByDifficulty(String difficulty) {
        if (difficulty == null || difficulty.trim().isEmpty()) {
            throw new IllegalArgumentException("题目难度不能为空");
        }
        if (!"easy".equals(difficulty) && !"medium".equals(difficulty) && !"hard".equals(difficulty)) {
            throw new IllegalArgumentException("题目难度无效，必须是 easy/medium/hard");
        }
        return problemMapper.findByDifficulty(difficulty);
    }

    /**
     * 创建新题目（管理员功能）
     */
    public Problem createProblem(Problem problem) {
        if (problem.getId() != null) {
            throw new IllegalArgumentException("新题目不能指定ID");
        }
        // 校验必填字段
        if (problem.getTitle() == null || problem.getTitle().trim().isEmpty()) {
            throw new IllegalArgumentException("题目标题不能为空");
        }
        if (problem.getDifficulty() == null || problem.getDifficulty().trim().isEmpty()) {
            throw new IllegalArgumentException("题目难度不能为空");
        }

        if (problem.getTimeLimit() == null) {
            problem.setTimeLimit(1.0);
        }
        if (problem.getMemoryLimit() == null) {
            problem.setMemoryLimit(262144);
        }

        problemMapper.insert(problem);
        return problem;
    }

    /**
     * 更新题目（管理员功能）
     */
    public boolean updateProblem(Problem problem) {
        if (problem.getId() == null || problem.getId() <= 0) {
            throw new IllegalArgumentException("更新题目ID无效");
        }
        int ret = problemMapper.update(problem);
        if (ret == 0) {
            throw new RuntimeException("更新题目不存在: " + problem.getId());
        }
        return true;
    }

    /**
     * 删除题目（管理员功能）
     */
    public boolean deleteProblem(Long id) {
        if (id == null || id <= 0) {
            throw new IllegalArgumentException("删除题目ID无效");
        }
        int ret = problemMapper.deleteById(id);
        if (ret == 0) {
            throw new RuntimeException("删除题目不存在: " + id);
        }
        return true;
    }

    /**
     * 获取题目总数
     */
    public int getTotalCount() {
        return problemMapper.count();
    }
}

```

九点三十下班开始补题，以下是总结（AI 写了一半）

**今天Service层工作总结：**

**ProblemService** - 全部搞定

- getProblems：分页 + 参数校验
- getProblemById：id 合法性检查 + null 判断
- getProblemsByDifficulty：修了字符串比较 bug（`!=` 改 .equals()）
- createProblem：必填字段校验 + 默认值设置 + 重新查询补全时间戳
- updateProblem：优化到只查一次数据库
- deleteProblem：简单粗暴

**SubmissionService** - 基本完成，还有小问题

- submitCode：参数校验齐全 + 异步调用判题（传 id 而不是对象，避免循环依赖）
- getSubmissionById：查询提交
- getUserSubmissions/getProblemSubmissions：这两个不应该在查不到时抛异常，应该返回空列表
- getRecentSubmissions：分页逻辑
- updateJudgeResult：状态校验可以优化（用 Set.of 代替一堆 &&）

**总体**：两个 Service 的主体逻辑都完成了，参数校验和错误处理也铺上了。就是有些细节逻辑的边界情况处理还需调整。

**小巧思**

```java
problemMapper.insert(problem);
return problemMapper.findById(problem.getId());
```

创建 problem 的时候这样写，可以让 return 的 problem 对象带有完整的时间戳等属性，非常优雅。