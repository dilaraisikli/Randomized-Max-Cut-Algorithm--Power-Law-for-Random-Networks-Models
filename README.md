$$ \mathbf{Randomized\ Max\ Cut\ and\ Preferential\ Attachment\ Graph\ Models} $$  
### Stat4DS – Homework 01  
**Authors:** Dilara Isıklı, Mert Yıldız  

---

$$ \mathbf{1.\ Overview} $$

This project covers:

- Randomized Max-Cut algorithm  
- Erdős–Rényi graph simulations  
- Preferential attachment graph growth  
- Degree distributions, power-law fitting, CCDF  
- Full R implementation and analysis  

---

$$ \mathbf{2.\ Part\ A\ --\ Randomized\ Max\ Cut} $$

### $$ \mathbf{2.1\ Problem\ Setup} $$

We analyze Max-Cut on Erdős–Rényi graphs:

- Graph generated as \( G(n, p=0.1) \)
- True optimal cut:
  $$
  \text{opt}(G)
  $$
- 1000 random Max-Cut simulations
- Compare with the theoretical guarantee:
  $$
  \mathbb{E}[\text{cut}] \ge \frac{\text{opt}(G)}{2}
  $$

---

### $$ \mathbf{2.2\ Implemented\ Functions} $$

**True opt(G):**

```r
get_opt_function <- function(G) {
  B <- get.adjacency(G, sparse = FALSE)
  abs(maxcut(B)$pobj)
}
```

**Randomized Max-Cut algorithm:**

```r
randomized_maxCut_algorithm <- function(V) {
  U <- list()
  for (i in 1:length(V)) {
    if (sample(c(0,1),1) == 1) U <- c(U, V[i])
  }
  return(U)
}
```

**Cut-size function:**

```r
card_U <- function(new_U, edges_list) {
  cardinality <- 0
  for (i in seq(1, length(edges_list), 2)) {
    if (is.element(edges_list[i], new_U) & 
       !is.element(edges_list[i+1], new_U)) cardinality <- cardinality + 1
    if (is.element(edges_list[i+1], new_U) & 
       !is.element(edges_list[i], new_U)) cardinality <- cardinality + 1
  }
  return(cardinality)
}
```

**Comparison with theoretical bound:**

```r
compare_cutsize_withBound <- function(avg, opt) {
  if (avg >= opt/2) print("Average cut ≥ opt(G)/2")
  else print("Average cut < opt(G)/2")
}
```

---

### $$ \mathbf{2.3\ Experiments\ with\ Graphs\ of\ Size\ 50,\ 70,\ 30} $$

For each graph:

- Compute opt(G)
- Run 1000 random Max-Cut iterations
- Plot graph, histogram, summary statistics

**Observations:**

- Average cut ≈ Normal distribution  
- Rarely reaches opt(G)  
- Larger graphs increase runtime  
- The guarantee  
  $$
  \overline{\text{cut}} \ge \frac{\text{opt}(G)}{2}
  $$  
  always holds  

---

### $$ \mathbf{2.4\ Summary\ of\ Part\ A} $$

- Algorithm performs as expected  
- All tested graphs satisfy the theoretical performance bound  
- Optimal cut almost never appears  
- Runtime grows significantly with graph size  

---

$$ \mathbf{3.\ Part\ B\ --\ Preferential\ Attachment\ Graph\ Growth} $$

We create a graph that grows by adding vertices according to:

- With probability \( 0.5 \): attach to a **random vertex**
- With probability \( 0.5 \): attach to a vertex chosen by **in-degree**

This produces degree patterns similar to scale-free networks.

---

### $$ \mathbf{3.1\ Growth\ Model} $$

**Initial graph:**  
A directed 4-node cycle: \( 1 \rightarrow 2 \rightarrow 3 \rightarrow 4 \rightarrow 1 \)

**For each new vertex:**

1. Draw \( U \sim \text{Unif}(0,1) \)
2. If \( U > 0.5 \): connect randomly  
3. If \( U \le 0.5 \): connect proportional to in-degree  

---

### $$ \mathbf{3.2\ Degree\ Distribution\ and\ Power\ Law} $$

Compute degree distribution:

```r
deg_G <- degree(G, mode = "total")
```

Fit a power law:

```r
fit <- fit_power_law(deg_G)
```

**Observations:**

- Log–log degree distribution is heavy-tailed  
- Power-law fits reasonably well  
- Typical behavior of preferential attachment networks  

---

### $$ \mathbf{3.3\ CCDF\ on\ Log\ Log\ Scale} $$

We compute:

$$
\text{CCDF}(k) = \mathbb{P}(\deg \ge k)
$$

This shows a straight decaying tail → consistent with power-law behavior.

---

### $$ \mathbf{3.4\ Multiple\ Trials} $$

Repeated 3 independent trials:

- Similar power-law exponent  
- Similar CCDF curves  
- Robust structural behavior  

---

$$ \mathbf{4.\ Final\ Summary} $$

### **Part A – Max-Cut**
- Randomized Max-Cut consistently satisfies  
  $$
  \mathbb{E}[\text{cut}] \ge \frac{\text{opt}(G)}{2}
  $$
- Distributions nearly Gaussian  
- Optimal cut almost never reached  

### **Part B – Preferential Attachment**
- Produces a heavy-tailed, nearly scale-free network  
- Degree distribution follows a power-law  
- CCDF confirms heavy tail  
- Repeated trials consistent  

---
