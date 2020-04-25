---
layout: post
title: Ứng dụng của hai thuật toán duyệt đồ thị cơ bản
category : computer science
permalink: 2017/04/18/ung-dung-dfs-va-bfs/
tagline: "Supporting tagline"
tags : [graph, algorithm, depth-first-search, breadh-first-search, application, bipartite, shortest-path]
mathjax: true
---

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

<h3><a name="1-gioi-thieu"> 1. Giới thiệu</a></h3>

Trong cấu trúc dữ liệu và giải thuật thì đồ thị là một dạng cấu trúc dữ liệu cơ bản và có ứng dụng rất rộng rãi. Nhiều hệ thống phức tạp có thể được mô phỏng như là một đồ thị, ví dụ như hệ thống giao thông, hệ thống mạng, hệ thống truyền tải điện. Trong đó, hai thuật toán duyệt đồ thị theo chiều rộng (BFS) và chiều sâu (DFS) là hai thuật toán cơ bản nhất của đồ thị. Các thuật toán này giúp chúng ta "đến thăm" tất cả các cạnh và các đỉnh của đồ thị trong thời gian tối thiểu. 

Trong bài viết này, tôi muốn đi sâu hơn bằng cách giới thiệu những ứng dụng của hai thuật toán này trong lập trình. Phần cuối bài, tôi sẽ liên hệ các thuật toán này với một trò chơi **Dò mìn (Minesweeper)**  một trò cổ điền của Microsoft, và giới thiệu với các bạn bản <a name="remake" href="http://msp-online.herokuapp.com/index.html">**Dò mìn remake**</a> của tôi.

<a name="bai-toan-co-su-dung-bfs"></a>
<h3>2. Bài toán có sử dụng BFS</h3>

<a name="kiem-tra-do-thi-phan-doi"></a>
<h4>2.1 Kiểm tra một đồ thị là phân đôi (bi-partite)</h4>

Một đồ thị là phân đôi, hay hai phía là một đồ thị \\(G = (V, E)\\) trong đó tập hợp các đỉnh có thể được chia làm hai tập hợp không giao nhau sao cho các đỉnh ở cùng nhóm không kề nhau. Có rất nhiều tình huống thực tế có thể mô phỏng bằng đồ thị phân đôi. Ví dụ, muốn biểu diễn mối quan hệ giữa một nhóm học sinh và một nhóm các trường học, ta có thể dùng một đồ thị với mỗi một học sinh và mỗi trường học là một đỉnh. Giữa một người \\(A\\) và một trường \\(X\\) sẽ có một cạnh nếu như \\(A\\) đã hoặc đang đi học ở trường \\(X\\). Kiểu đồ thị này sẽ là một đồ thị phân đôi, với một nhóm đỉnh là người và nhóm kia là trường; sẽ không có cạnh nối giữa hai người hoặc giữa hai trường.

Một tính chất thú vị của đồ thị phân đôi là, ta có thể tô màu các đỉnh đồ thị với hai màu sao cho không có hai đỉnh nào cùng màu kề nhau. 

<div class="imgcap">
	<center><img src ="/assets/images/bipartite.png" width = "50%" align="middle"/></center>
	<div class="thecap"> Đồ thị phân đôi <br></div>
</div>

Muốn kiểm tra một đồ thị là phân đôi hay không, ta có thể dùng BFS để tô màu đồ thị với hai màu, đen và đỏ. 
* Ta bắt đầu từ một đỉnh bất kỳ và tô màu đen cho đỉnh đó. 
* Với mỗi đỉnh \\(u\\) kề với đỉnh đang xét \\(v\\), nếu đỉnh \\(u\\) đó là đỉnh mới, ta sẽ tô màu \\(u\\) ngược với màu của \\(v\\); nếu \\(v\\) là màu đỏ, ta sẽ  tô \\(u\\) màu đen và ngược lại. 
* Nếu \\(u\\) đã được thăm trước đó và có màu trùng với \\(v\\), ta sẽ dừng thuật toán và kết luận đồ thị không phải đồ thị phân đôi. 
* Cuối cùng, nếu ta có thể tô màu tất cả các đỉnh mà không vi phạm quy tắc tô màu, ta có thể kết luận đồ thị là phân đôi.

~~~~java
public static boolean isBipartite(Map<Integer, List<Integer>> map, int source){
	Queue<Integer> q = new LinkedList<Integer>();
	int[] states = new int[graph.size()+1]; // theo mac dinh, states[i] = 0, tuc la chua tham
	int[] colors = new int[graph.size()+1]; // luu du lieu ve mau, 1 la den, 0 la do, -1 la chua to mau
	for(int i = 0; i < colors.length; i++){
		colors[i] = -1; // luc dau tat ca dinh deu chua to mau
	}
	q.offer(s); // them s vao dau cua queue
	color[s] = 1; // to mau den cho diem xuat phat
	states[s] = 1; // chuyen trang thai cua s thang 'da tham'
	while(!q.isEmpty()){
		Integer current = q.poll(); 
		List<Integer> adjList = graph.get(current);
		if(adjList != null){
			for(int k : adjList){
				if (states[k] == 0){
					states[k] = 1;
					colors[k] = colors[current] == 1 ? 0 : 1;
					q.offer(k);
				}else if (colors[k] == colors[current]){
					return false; // 2 dinh ke nhau co cung mau, ko phai bipartite
				}
			}
		}
	}
	return true; 
}
~~~~

Ta có thể thâý, bằng cách thêm một array để tô màu các đỉnh vào BFS, ta có thể kiểm tra xem một đồ thị có phải phân đôi không. Ngoài ra, ta có thể rút ra nhận xét, các đồ thị có vòng chẵn là đồ thị hai phiá, còn vòng lẻ thì không.

<h4>2.2 Tìm đường ngắn nhất trong đồ thị không có trọng số (Single source Shortest path in an unweighted graph)</h4>

Bài toán tìm đường ngắn nhất trong đồ thị là một dạng bài toán rất rộng, với rất nhiều thuật toán nổi tiếng áp dụng cho các loại đồ thị khác nhau. Trong thực tế, các thuật toán tìm đường ngắn nhất có rất nhiều ứng dụng trong công nghệ, ví dụ như tìm đường ngắn nhất để truyền tín hiệu trong mạng internet. Trong phần nay, tôi sẽ đề cập đến bài toán tìm đường ngắn nhất từ một đỉnh đến nhiều đỉnh trong đồ thị với các cạnh không có trọng số. 

<b>Đường ngắn nhất từ một đỉnh đến một đỉnh khác</b> là đường đi từ đỉnh thứ nhất đến đỉnh thứ hai sao cho tổng độ dài (weight) của đoạn đường đó là nhỏ nhất. Trong đồ thị không trọng số với các cạnh có độ dài bằng nhau, đường ngắn nhất là đường đi qua ít cạnh nhất.

Thuật toán BFS xuất phát từ đỉnh \\(s\\) sẽ giúp ta tìm đường ngắn nhất từ \\(s\\) đến tất cả các đỉnh liên thông với \\(s\\). Muốn biết tại sao BFS lại có thể tìm đựơc đường đi qua ít cạnh nhất từ đỉnh này đến đỉnh khác trong đồ thị, ta quay trở lại khái niệm của BFS một chút. Trong BFS, từ một đỉnh hiện tại, ta luôn đi thăm tất cả các đỉnh kề với nó trước, sau đó thăm tất cả các đỉnh cách nó một đỉnh, rồi các đỉnh cách nó hai đỉnh... vân vân. Như vậy, nếu từ một đỉnh \\(u\\) ta chạy BFS, quãng đường đên đỉnh \\(v\\) luôn là quãng đường đi qua ít cạnh nhất. 

* Để dùng BFS tìm đường ngắn nhất từ đỉnh \\(s\\) đến các, ta viết như BFS cơ bản và thêm vào một array, đặt tên là \\(parent\\), với ý nghĩa \\(parent[v] = u\\) là: \\(u\\) và \\(v\\) là hai đỉnh kề nhau và BFS đến thăm \\(v\\) từ \\(u\\). Nói một cách khác, \\(parent[i]\\) lưu đỉnh trước của \\(i\\).
* Như vậy, \\(parent[s]\\) sẽ không có vì \\(s\\) là đỉnh đầu tiên đựơc thăm. Sau khi duyệt BFS ta sẽ thu đựơc \\(parent\\) với thứ tự đi thăm. 
* Muốn biết đường ngắn nhất từ \\(s\\) tới một đỉnh \\(u\\) bất kỳ, ta xuất phát từ \\(parent[u]\\) rồi dò ngược trở lại cho đến khi quay về \\(s\\), đó là đường ngắn nhất từ \\(s\\) tới \\(u\\).
* Trong ví dụ, ta bắt đầu từ \\(1\\), 1 sẽ là đỉnh trước của \\(5, 2, 6\\); \\(2\\) sẽ là đỉnh trước của \\(3, 4\\). Vì thế đường ngắn nhất từ \\(1\\) đến \\(4\\) sẽ là \\(1 \rightsquigarrow 2 \rightsquigarrow 4\\).

<div class="imgcap">
	<center><img src ="/assets/images/bfs_shortest.png" width = "100%" align="middle"/></center>
	<div class="thecap"> Tìm đường ngắn nhất từ 1 với BFS <br></div>
</div>


~~~~java
public static int[] shortestPath(Map<Integer, List<Integer>> map, int source){
	Queue<Integer> q = new LinkedList<Integer>();
	int[] states = new int[graph.size()+1]; // theo mac dinh, states[i] = 0, tuc la chua tham
	int[] parents = new int[graph.size()+1]; // luu dinh truoc
	for(int i = 0; i < colors.length; i++){
		parents[i] = -1; // theo mac dinh, dinh truoc cua cac canh se la -1
	}
	q.offer(s); // them s vao dau cua queue
	states[s] = 1; // chuyen trang thai cua s thang 'da tham'
	while(!q.isEmpty()){
		Integer current = q.poll(); 
		List<Integer> adjList = graph.get(current);
		if(adjList != null){
			for(int k : adjList){
				if (states[k] == 0){
					states[k] = 1;
					// ta di tham k tu current
					parents[k] = current; 
					q.offer(k);
				}
			}
		}
	}
	return parents; 
}
~~~~

<h3>3. Bài toán có sử dụng DFS</h3>

<h4>3.1 Tìm vòng trong đồ thị vô hướng</h4>

Ta có thể lợi dụng tính chất duyệt chiều sâu của DFS để tìm xem đồ thị có vòng không. Trong đồ thị, vòng là một đường đi với mỗi cạnh chỉ đựơc đi qua một lần và đỉnh xuất phát cũng là đỉnh cuối cùng. Với đồ thị vô hướng, vòng tồn tại khi và chỉ khi, từ một đỉnh, ta gặp lại một đỉnh đã thăm mà không phải đỉnh trước của đỉnh đó. 

* Ví dụ, ta có đồ thị \\(1 - 2 - 3\\), ta xuất phát từ \\(1\\) và hiện tại đang xét đỉnh \\(2\\). Khi kiểm tra các đỉnh kề với \\(2\\), ta thâý có \\(1\\) đã thăm nhưng vì \\(1\\) là đỉnh trước của \\(2\\) nên ta không kết lụân là đồ thị có vòng.

* Trong một ví dụ khác, ta có đồ thị \\(1 - 2 - 3 - 1\\). Ta xuất phát từ \\(1\\) và hiện tại đang xét \\(3\\). Ta thâý \\(1\\) kề với \\(3\\) nhưng \\(1\\) lại không phai đỉnh trước của \\(3\\). Ta có thể kết luận đồ thị này có vòng. 

Ta có thể viết thuật toán như sau:

~~~~java
// kiem tra vong trong do thi vo huong
public static boolean hasCycleHelper(Map<Integer, List<Integer>> graph, int[] parents, int[] states, int s){
	states[s] = 1; // states = 0 la chua tham, 1 la da tham
	for(int neighbor : graph.get(s)){
		if (states[neighbor] == 0){
			parents[neighbor] = s;
			if(hasCycleHelper(graph, parents, states, neighbor)){
				return true;
			}
		}
		else if (states[neighbor] == 1 && parents[s] != neighbor){
			return true;
		}
	}
	return false;
}

public static boolean hasCycle(Map<Integer, List<Integer>> graph){
	int[] states = new int[grah.size() + 1];
	for(int v : graph.keySet()){
		if (states[v] == 0 && hasCycleHelper(graph, states, v)){
			return true;
		}
	}
	return false;
}
~~~~

<h4>3.2 Tìm vòng (cycle) trong đồ thị có hướng</h4>

Giống với đồ thị vô hướng, ta cũng có thể dùng DFS để xác định đồ thị cớ hướng có vòng không. Tuy nhiên, trong đồ thị có hướng, điều kiện có vòng khác với vô hướng. Giả sử có đồ thị \\(1 \rightarrow 2 \rightarrow 3; 1 \rightarrow 3 \\). Ta duyệt DFS xuất phát từ \\(1\\) và đi theo đường \\(1 \\rightarrow 2 \\rightarrow 3\\). Đến đây, ta quay ngược lại và đi theo đường \\(1 \rightarrow 3\\). Mặc dù từ đỉnh \\(1\\) ta quay lại đỉnh \\(3\\) là đỉnh đã thăm và \\(3\\) không phải đỉnh trước của \\(1\\), nhưng rõ ràng đồ thị này không hề có vòng. 

<div class="imgcap">
	<center><img src ="/assets/images/cycle_directed.png" width = "80%" align="middle"/></center>
	<div class="thecap"> Ví dụ về vòng trong đồ thị có hướng <br></div>
</div>

Muốn kiểm tra vòng trong đồ thị có hướng với DFS, ta cần phải mở rộng DFS, tăng số trạng thái của một đỉnh lên 3 chứ không phải 2. Cụ thể như sau: 

* Trạng thái 0: Chưa thăm
* Trạng thái 1: Đỉnh này đã thăm, nhưng ta chưa đi hết các đỉnh trong nhánh của nó. Nói một cách khác, ta đang trong qúa trình duyệt DFS bắt đầu từ đỉnh này. Ví dụ, ta có đồ thị \\(1 \rightarrow 2 \rightarrow 3\\), ta bắt đầu duyệt DFS từ đỉnh \\(1\\), và hiện tại đang xét các đỉnh kề với đỉnh \\(2\\). Do ta chưa xét hết các đỉnh trong nhánh bắt đầu từ \\(1\\), ta chưa thể đánh dấu \\(1\\) là hoàn tất.
* Trạng thái 2: Qúa trình duyệt DFS của đỉnh này đã hoàn tất. Trong đồ thị \\(1 \rightarrow 2 \rightarrow 3\\), khi ta hoàn thành xem xét các đỉnh \\(2, 3\\), ta có thể đánh dấu \\(1\\) là đã xong.
* Trong nhiều tài liệu, người ta dùng màu để đánh dấu, trạng thái 0 là **trắng**, 1 là **xám** và 2 là **đen**. 
* Với 3 trạng thái, khi duyệt DFS, nều từ một đỉnh ta đến đỉnh kế tiếp, nếu đỉnh kế tiếp đã đựơc thăm nhưng chưa hoàn tất (trạng thái 1, hay xám), đồ thị đó là có vòng.

~~~~java
public static boolean helper(Map<Integer, List<Integer>> graph, int[] states, int s){
	states[s] = 1; // states = 0 la chua tham, 1 la da tham, 2 la hoan tat
	boolean hasCycle = false;
	for(int neighbor : graph.get(s)){
		if (states[neighbor] == 0){ // chua tham
			if (helper(graph, parents, states, neighbor)){
				return true;
			}
		}
		else if (states[neighbor] == 1){
			return true;
		}
	}
	states[s] = 2; // hoan tat xet duyet tat ca nhanh cua s
	return false;
}

public static boolean hasCycle(Map<Integer, List<Integer>> graph){
	int[] states = new int[grah.size() + 1];
	for(int v : graph.keySet()){
		if (states[v] == 0 && helper(graph, states, v)){
			return true;
		}
	}
	return false;
}
~~~~

Trong duyệt thuật toán trên với các đồ thị sau để thâý rõ hơn. Ta xuất phát từ \\(1\\). Đồ thị thứ nhất không có vòng, đồ thị thứ hai có, khi mà từ đinh \\(3\\) chúng ta quay lại \\(1\\) vẫn còn ở trạng thái 'chưa xong'.

<div class="imgcap">
	<center><img src ="/assets/images/dfs_cycle1.png" width = "100%" align="middle"/></center>
	<div class="thecap"> Ví dụ về trường hợp không có vòng<br></div>
</div>
<div class="imgcap">
	<center><img src ="/assets/images/dfs_cycle2.png" width = "100%" align="middle"/></center>
	<div class="thecap"> Ví dụ về trường hợp có vòng <br></div>
</div>

<h3>4. Case study: Dò mìn (Minesweeper)</h3>
* Trong phần này, tôi muốn liên hệ các thuật toán DFS và BFS với một trò chơi mà chắc nhiều bạn đã biết.
* Dò mìn (Minesweeper) của Microsoft là một trò chơi nổi tiếng. Phiên bản ban đầu của Dò mìn là một bản đồ hình chữ nhật \\(N\\) x \\(M\\). Mỗi ô có hoặc không có mìn.
* Ban đầu các ô đều chưa đựơc lật, mỗi lựơt ta click chuột để lật. Nếu ta click vào mìn thì thua. Ta thắng khi lật hết tất cả các ô không có mìn.
* Khi lật các ô không có mìn, ta sẽ thâý các số từ \\(1\\) đến \\(8\\), là số mìn xung quanh ô ta vừa lật. 
* Tuy nhiên, **khi lật các ô mà xung quanh không có mìn (có \\(0\\) quả), ta sẽ cùng một lúc lật tất cả các ô xung quanh cho tới khi ta chạm đến các ô mà xung quanh nó có mìn**. Các ô này sẽ đựơc bỏ trống, không đánh số. 

<div class="imgcap">
	<center><img src ="/assets/images/msp1.png" width = "20%" align="middle"/></center>
	<div class="thecap"> Dò mìn <br></div>
</div>

* Nếu coi map của trò chơi là một đồ thị, thì mỗi ô là một đỉnh, với mỗi đỉnh kết nối với các đinh ở trên, dưới, trái, phải của nó. Làm thế nào viết thuật toán mở một ô không có mìn, khi được mở sẽ tự động mở tất cả các ô xung quanh? Đây là chỗ dùng đến DFS và BFS. 
* Khi mở một ô không có mìn, ta có thể dùng DFS hoặc BFS để loang ra xung quanh cho tới khi chạm phải các ô có mìn xung quanh. Ví dụ:

~~~~python
# v la dinh hien tai dang xet
function open(graph, v):
	states[v] = 1 # state = 1 la da tham, 0 la chua tham
	# neu xung quanh v khong co min, ta bat dau dung DFS de mo? tiep cac o xung quanh
	if (numMinesArround(v) == 0):
		for u in neighbor(v):
			if states[u] == 0:
				open(graph, u)
~~~~

* Các bạn có thể chơi Dò mìn tại đây: <a href="http://msp-online.herokuapp.com/index.html">**http://msp-online.herokuapp.com/index.html**</a>. Game có nhiều cấp độ khác nhau, từ rất dễ đến rất khó, đảm bảo sẽ giúp các bạn giết thời gian một cách hiệu qủa.

<h3>Tham khảo</h3>
[1] Cormen, Thomas H., Leiserson, Charles E., Rivest,Ronald L., and Stein, Clifford. "Introduction to Algorithms", MIT Press(2009)

[2] Skiena, Steven. "The Algorithm Design Manual", Springer(2008)