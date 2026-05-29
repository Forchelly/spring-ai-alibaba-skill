---
title: "subgraph-as-nodeaction"
url: "/docs/frameworks/graph-core/examples/subgraph-as-nodeaction"
---

# 子图作为节点操作

在 Spring AI Alibaba Graph 中，可以将一个完整的 Graph 作为另一个 Graph 的节点，实现工作流的模块化设计。

## 概念

将子图作为 `NodeAction` 使用，可以：

- **模块化**: 将复杂工作流拆分为可重用的子模块

- **封装**: 隐藏子图的内部实现细节

- **组合**: 构建层次化的工作流结构

## 基本示例

### 定义子图

定义子图[查看完整代码](https://github.com/alibaba/spring-ai-alibaba/tree/main/examples/documentation/src/main/java/com/alibaba/cloud/ai/examples/documentation/graph/examples/SubgraphAsNodeActionExample.java)
```java
import com.alibaba.cloud.ai.graph.CompiledGraph;
import com.alibaba.cloud.ai.graph.KeyStrategy;
import com.alibaba.cloud.ai.graph.KeyStrategyFactory;
import com.alibaba.cloud.ai.graph.StateGraph;
import com.alibaba.cloud.ai.graph.exception.GraphStateException;
import com.alibaba.cloud.ai.graph.state.strategy.ReplaceStrategy;

import java.util.HashMap;
import java.util.Map;

import static com.alibaba.cloud.ai.graph.StateGraph.END;
import static com.alibaba.cloud.ai.graph.StateGraph.START;
import static com.alibaba.cloud.ai.graph.action.AsyncNodeAction.node_async;

/**
* 定义子图
*/
public static CompiledGraph createSubGraph(KeyStrategyFactory keyStrategyFactory) throws GraphStateException {
  StateGraph subGraph = new StateGraph(keyStrategyFactory)
          .addNode("substep1", node_async(state -> {
          String input = (String) state.value("input").orElse("");
          return Map.of("result", "SubStep1:" + input);
      }))
          .addNode("substep2", node_async(state -> {
          String prev = (String) state.value("result").orElse("");
          return Map.of("result", prev + "->SubStep2");
      }))
          .addEdge(START, "substep1")
      .addEdge("substep1", "substep2")
          .addEdge("substep2", END);

  return subGraph.compile();
}
```

### 将子图包装为 NodeAction

将子图包装为 NodeAction[查看完整代码](https://github.com/alibaba/spring-ai-alibaba/tree/main/examples/documentation/src/main/java/com/alibaba/cloud/ai/examples/documentation/graph/examples/SubgraphAsNodeActionExample.java)
```java
import com.alibaba.cloud.ai.graph.CompiledGraph;
import com.alibaba.cloud.ai.graph.OverAllState;
import com.alibaba.cloud.ai.graph.RunnableConfig;
import com.alibaba.cloud.ai.graph.action.NodeAction;

import java.util.Map;
import java.util.Optional;

/**
* 将子图包装为 NodeAction
*/
public static class SubGraphNode implements NodeAction {

  private final CompiledGraph subGraph;

  public SubGraphNode(CompiledGraph subGraph) {
      this.subGraph = subGraph;
  }

  @Override
  public Map<String, Object> apply(OverAllState state) {
      // 从父状态提取子图需要的数据
      String input = (String) state.value("data").orElse("");

      // 执行子图
      Map<String, Object> subInput = Map.of("input", input);
      Optional<OverAllState> subResult = subGraph.invoke(subInput, RunnableConfig.builder().build());

      // 返回结果给父图
      String result = (String) subResult.get().value("result").orElse("");
      return Map.of("processed", result);
  }
}
```

### 在父图中使用

在父图中使用[查看完整代码](https://github.com/alibaba/spring-ai-alibaba/tree/main/examples/documentation/src/main/java/com/alibaba/cloud/ai/examples/documentation/graph/examples/SubgraphAsNodeActionExample.java)
```java
import com.alibaba.cloud.ai.graph.CompiledGraph;
import com.alibaba.cloud.ai.graph.StateGraph;

import static com.alibaba.cloud.ai.graph.StateGraph.END;
import static com.alibaba.cloud.ai.graph.StateGraph.START;
import static com.alibaba.cloud.ai.graph.action.AsyncNodeAction.node_async;

/**
* 在父图中使用
*/
public static CompiledGraph useInParentGraph(KeyStrategyFactory keyStrategyFactory, CompiledGraph subGraph) throws GraphStateException {
SubGraphNode subGraphNode = new SubGraphNode(subGraph);

StateGraph parentGraph = new StateGraph(keyStrategyFactory)
          .addNode("prepare", node_async(state -> {
      return Map.of("data", "Input Data");
  }))
          .addNode("process", node_async(subGraphNode))  // 使用子图作为节点
          .addNode("finalize", node_async(state -> {
      String processed = (String) state.value("processed").orElse("");
      return Map.of("final", "Final:" + processed);
  }))
          .addEdge(START, "prepare")
  .addEdge("prepare", "process")
  .addEdge("process", "finalize")
          .addEdge("finalize", END);

  return parentGraph.compile();
}
```

### 执行父图

执行父图[查看完整代码](https://github.com/alibaba/spring-ai-alibaba/tree/main/examples/documentation/src/main/java/com/alibaba/cloud/ai/examples/documentation/graph/examples/SubgraphAsNodeActionExample.java)
```java
// 执行
OverAllState result = compiledParent.invoke(Map.of());
System.out.println("Final result: " + result.value("final").orElse(""));

// 输出: Final result: Final:SubStep1:Input Data->SubStep2
```

## 高级示例 - 带参数的子图

带参数的子图[查看完整代码](https://github.com/alibaba/spring-ai-alibaba/tree/main/examples/documentation/src/main/java/com/alibaba/cloud/ai/examples/documentation/graph/examples/SubgraphAsNodeActionExample.java)
```java
// 可配置的子图节点
public class ConfigurableSubGraphNode implements NodeAction {

  private final CompiledGraph subGraph;
  private final String inputKey;
  private final String outputKey;

  public ConfigurableSubGraphNode(
      CompiledGraph subGraph,
      String inputKey,
      String outputKey
  ) {
      this.subGraph = subGraph;
      this.inputKey = inputKey;
      this.outputKey = outputKey;
  }

  @Override
  public Map<String, Object> apply(OverAllState state) {
      // 从父状态读取指定键的数据
      Object input = state.value(inputKey).orElse(null);

      // 执行子图
      OverAllState subResult = subGraph.invoke(Map.of("input", input));

      // 将结果写入指定键
      Object output = subResult.value("result").orElse(null);
      return Map.of(outputKey, output);
  }
}

// 使用
CompiledGraph subGraph = createSubGraph(keyStrategyFactory);
NodeAction configurableNode = new ConfigurableSubGraphNode(
  subGraph,
  "userInput",    // 从父状态读取 userInput
  "processedOutput"  // 写入 processedOutput
);
```

## 状态映射

处理父子图之间的状态映射：

状态映射[查看完整代码](https://github.com/alibaba/spring-ai-alibaba/tree/main/examples/documentation/src/main/java/com/alibaba/cloud/ai/examples/documentation/graph/examples/SubgraphAsNodeActionExample.java)
```java
public class SubGraphNodeWithMapping implements NodeAction {

  private final CompiledGraph subGraph;
  private final Function<OverAllState, Map<String, Object>> inputMapper;
  private final BiFunction<OverAllState, OverAllState, Map<String, Object>> outputMapper;

  public SubGraphNodeWithMapping(
      CompiledGraph subGraph,
      Function<OverAllState, Map<String, Object>> inputMapper,
      BiFunction<OverAllState, OverAllState, Map<String, Object>> outputMapper
  ) {
      this.subGraph = subGraph;
      this.inputMapper = inputMapper;
      this.outputMapper = outputMapper;
  }

  @Override
  public Map<String, Object> apply(OverAllState parentState) {
      // 使用 mapper 转换父状态到子状态
      Map<String, Object> subInput = inputMapper.apply(parentState);

      // 执行子图
      OverAllState subResult = subGraph.invoke(subInput);

      // 使用 mapper 转换子状态到父状态
      return outputMapper.apply(parentState, subResult);
  }
}
```

## 应用场景

- **数据处理管道**: 将数据清洗、转换、验证等步骤封装为独立子图

- **微服务编排**: 每个微服务调用封装为子图

- **条件处理**: 根据不同条件选择不同的子图执行

- **并行处理**: 多个子图并行执行不同任务

- **版本控制**: 不同版本的处理逻辑封装为不同子图

## 附图

![png](https://java2ai.com/assets/images/subgraph-as-nodeaction_15_0-f81f98566aa3b6806d961197176bbd24.png)
## 相关文档

[子图作为 StateGraph](/docs/frameworks/graph-core/examples/subgraph-as-stategraph) - 另一种子图集成方式

- [子图作为 CompiledGraph](/docs/frameworks/graph-core/examples/subgraph-as-compiledgraph) - 编译后的子图使用

- [快速入门](/docs/frameworks/graph-core/quick-start) - Graph 基础使用