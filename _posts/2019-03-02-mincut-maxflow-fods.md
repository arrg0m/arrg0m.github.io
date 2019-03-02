---
title: Non-trivial example for "Community Finding and Graph Partitioning"
date: 2019-03-02 18:00:00 +0900
categories: graph
---

{% include lib/mathjax.html %}

Avrim Blum, [John E. Hopcroft](https://www.cs.cornell.edu/jeh), Ravindran Kannan의 **Foundations of Data Science** 라는 책 - 아니 manuscript - 의 7장 11절의 내용에 대한 clarification을 해보려고 한다. 스터디하다가 이 부분에서 꽤 많은 논의가 이루어졌는데, 그런 부분에 대한 교통정리의 느낌으로 써내려간... 글인데 교통정리를 하기도 전에 양이 너무 많아져서 한 번 끊고 가야겠다.

---

스터디에서 지적된 바와 같이, 책에 나와 있는 그래프 7.6에 대해서는 non-trivial solution이 존재할 수 없다. 사실 그 이유는 원래 그래프인 $$G$$를 그려봤을 때 딱히 community라고 할만한 구조가 보이지 않는다는 점을 토대로 알 수 있다.

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_for_fig-7-6.png"| absolute_url }}" height=100 class="center">
<figcaption>Fig 1. 책 7.6의 그래프 H에 대응하는 원래 그래프 G.</figcaption>
</figure>

실제로 여기서 $$\lambda$$는 density, 즉 (weighted graph가 아닐 때) vertice 대비 edge의 수를 나타내는 만큼 비자명한 구조를 가지려면 꽤 큰 그래프가 필요할 거라고 생각, 다음과 같은 그래프를 생각해 보았다 (사실 3-clique을 가지는 그래프도 시도해봤는데, 망했다. 추측컨데 전체 그래프에서 edge의 개수와 vertex의 개수가 같거나 edge가 더 적은 경우에 *극단적인* 경우로 잘 빠져드는 게 아닐까 싶었다):

<figure>

<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_G.png" | absolute_url }}" height=200 class="center">
<figcaption>Fig 2. Nontrivial한 경우를 확인하기 위해 제시한 새로운 그래프 G.</figcaption>
</figure>

이 그래프 $$G = (V, E)$$는 $$V = \{a, b, c, d, e, f\}$$와 $$E = \{ab, bc, bd, be, cd, ce, de, ef\}$$로 이루어져 있는 unweighted, undirectional graph가 될 것이다. 여기서 induce되는 그래프 $$H$$는 다음과 같이 생겼다:

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_H.png" | absolute_url }}" height=300 class="center">
<figcaption>Fig 3. Fig 2.의 그래프 G에 대응되는 "augmented graph" H</figcaption>
</figure>

굳이 쓰자면, $$H=(V', E')$$이고, vertex와 (edge, weight)가 다음과 같이 이루어지는 그래프가 된다 (Notation은 대충 납득할 정도로만 썼다).

$$V' = \{s\} \cup E \cup V \cup \{t\} = \{s, ab, bc, bd, be, cd, ce, de, ef, a, b, c, d, e, f, t\}$$

$$E' = \{(se, 1) : e \in E\} \cup \{(ev, \infty): v \in e \in E\} \cup \{(vt, \lambda): v \in V\} = \cdots $$

먼저 이 그래프의 두 가지 trivial cut들을 살펴보자. 첫 번째는 source에서 자르는 다음과 같은 경우로, community가 구성되지 않는 ($$C = \phi$$) 경우에 대응된다. 해당되는 cut의 capacity는 $$8$$이다:

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_left.png" | absolute_url }}" height=200 class="center">
<figcaption>Fig 4. No community에 대응되는 trivial cut</figcaption>
</figure>

두 번째 trivial cut은 다음과 같이 sink 앞에서 자르는 경우로, 그래프 전체가 community가 되는 ($$C = V$$) 경우에 대응된다. 해당되는 cut의 capacity는 $$6\lambda$$가 될 것이다

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_right.png" | absolute_url }}" height=200 class="center">
<figcaption>Fig 5. Whole community에 대응되는 trivial cut</figcaption>
</figure>

그래프 $$H$$의 valid cut이 *Vertice-induced subgraph*에 대응된다는 점을 생각해 다음과 같이 community를 가지는 non-trivial cut들을 생각해볼 수 있을 것이다. 일부 경우에 대해서는 그래프를 다시 그리는게 편했을 것 같은데, 대충 그리니까 얼추 맞아서... 그냥 넘어갔다 (사실 2차원 bipartite한 경우에 불가능한 cut은 별로 없다. 굳이 cut으로 나타내려 해서 복잡해졌을 뿐, 그냥 색칠한다고 생각하면...)

1. abcde: $$1 + 5\lambda$$

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_abcde.png" | absolute_url }}" height=200 class="center">
</figure>

2. abdef: $$3 + 5\lambda$$

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_abdef.png" | absolute_url }}" height=200 class="center">
</figure>

3. abcd: $$ 4 + 4\lambda$$

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_abcd.png" | absolute_url }}" height=200 class="center">
</figure>

4. abde: $$ 4 + 4\lambda$$

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_abde.png" | absolute_url }}" height=200 class="center">
</figure>

5. bcde: $$2 + 4\lambda$$

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_bcde.png" | absolute_url }}" height=200 class="center">
</figure>

6. abc: $$6 + 3\lambda$$
<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_abc.png" | absolute_url }}" height=200 class="center">
</figure>

7. abd: $$ 6 + 3\lambda$$


<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_abd.png" | absolute_url }}" height=200 class="center">
</figure>

8. abe: $$ 6 + 3\lambda$$
<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_abe.png" | absolute_url }}" height=200 class="center">
</figure>

9. cde: $$ 5 + 3\lambda$$
<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_cde.png" | absolute_url }}" height=200 class="center">
</figure>

10. ab: $$ 7 + 2\lambda$$ - 두 점으로 이루어진 community는 항상 점 두개와 변 하나로 이루어져 있으니까.

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_ab.png" | absolute_url }}" height=200 class="center">
</figure>

12. c: singleton에 대응되는 finite capacity의 cut은 없다 (edge를 쓸 수 없어 s-E를 끊고 지나가지 못하니, 무슨 일이 있어도 무한대의 edge가 하나는 걸리게 될 것이다).

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/graph_cut_c.png" | absolute_url }}" height=200 class="center">
</figure>


일반적으로, cut의 capacity는 **포함되지 않는 변의 수 + $$\lambda \times$$ 포함되는 꼭짓점의 수**의 형태로 주어지니까, 직관적으로 적은 꼭짓점을 포함하면서 최대한 많은 변을 포함하는 subgraph가 좋은 점수를 받을 것이다. 위 12가지 경우 중 infeasible한 경우인 12번과 dominated되는 경우인 2 (이상 1.에 의해 dominated)), 3, 4 (이상 5에 의해 dominated), 6, 7, 8 (이상 9에 의해 dominated)를 제외한 나머지에 대해 lambda-minimum capacity 관계를 그려보면 대충 다음과 같이 나온다 (dominate된다는 뜻은, 같은 vertex set의 크기를 가지는 subset 중에서 min-cut의 candidate - 의 objective - 은 유일하다는 의미가 될 것이다. 물론 상수항에 의해, 가장 clique에 가까운 녀석일 것이고).

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/lambda_capacity.png" | absolute_url }}" height=300 class="center">
<figcaption>Fig 6(+a). $$\lambda$$에 대한 함수로 나타낸 개별 경우의 minimum capacity의 값.</figcaption>
</figure>

위 그림에서 찾을 수 있는, 각 $$\lambda$$에 대한 최적해는 다음과 같다:

* $$\lambda < 1$$: $$C = V$$ (모든 vertex가 community에 참여)
* $$1 \le \lambda < 2$$: $$C = \{b, c, d, e\}$$ (4-clique)
* $$2 \ge \lambda$$: $$C = \phi$$ (조건을 만족하는 community가 없음)

즉, 각 lambda에 대해 min-cut 문제를 풀 수 있다고 생각하면, 실제로 binary search를 통해 위 4-clique와 같은 구조를 찾을 수 있을 것이라 볼 수 있다. 그래프를 잘 만들면 lambda값에 따라 다양한 community가 나오는 경우도 만들 수 있을 것 같고.

---

사실 이것과 같이 다루려고 했던 다른 이슈중 하나는, min-cut의 natural dual problem인 max-flow로 이 문제를 해석하는 방법에 관한 것이었다. . [OR-tools](https://developers.google.com/optimization/flow/maxflow)와 [graph-tool](https://graph-tool.skewed.de/static/doc/flow.html?highlight=min_cut#graph_tool.flow.min_st_cut)을 시도헀는데, 전자는 non-integer weight를 넣을 수가 없어서 일단 [weight들에다 적당한 숫자를 곱한 상태로 돌려서](https://gist.github.com/arrg0m/7da9fe4cfc50d7c85b9d14cbfd6d4f15) 아래와 같은 해를 하나 구했고, 후자는 한심하게도 로컬에서 빌드를 실패했다.

<figure>
<img src="{{ "/assets/2019-03-02-mincut-maxflow-fods/max_flow.png" | absolute_url }}" height=300 class="center">
<figcaption>Fig 7(+a). 위 최적해의 경우에 대응되는 max-flow의 해.</figcaption>
</figure>

아마도 sink로 빠져나가는 edge들이 포화된 녀석들을 위주로 보면 좋을 것 같은데, 아직 감이 잘 잡히지는 않는다. 다만 max-flow로 해석하면 아주 작은 lambda나 아주 큰 lambda의 경우에는 trivial한 해가 나온다는게 조금 더 자명해진다. 아주 조금.
