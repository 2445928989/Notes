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