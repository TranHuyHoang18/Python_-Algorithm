class BTree(object):
  class Node(object):
    def __init__(self, t):
      self.keys = []
      self.children = []
      self.leaf = True
      self._t = t

    def split(self, parent, payload):
      new_node = self.__class__(self._t)

      mid_point = self.size//2
      split_value = self.keys[mid_point]
      parent.add_key(split_value)

      new_node.children = self.children[mid_point + 1:]
      self.children = self.children[:mid_point + 1]
      new_node.keys = self.keys[mid_point+1:]
      self.keys = self.keys[:mid_point]

      if len(new_node.children) > 0:
        new_node.leaf = False

      parent.children = parent.add_child(new_node)
      if payload < split_value:
        return self
      else:
        return new_node

    @property
    def _is_full(self):
      return self.size == 2 * self._t - 1

    @property
    def size(self):
      return len(self.keys)

    def add_key(self, value):
      self.keys.append(value)
      self.keys.sort()

    def add_child(self, new_node):
    
      i = len(self.children) - 1
      while i >= 0 and self.children[i].keys[0] > new_node.keys[0]:
        i -= 1
      return self.children[:i + 1]+ [new_node] + self.children[i + 1:]


  def __init__(self, t):
   
    self._t = t
    if self._t <= 1:
      raise ValueError("B-Tree must have a degree of 2 or more.")
    self.root = self.Node(t)

  def insert(self, payload):
   
    node = self.root
   
    if node._is_full:
      new_root = self.Node(self._t)
      new_root.children.append(self.root)
      new_root.leaf = False
      node = node.split(new_root, payload)
      self.root = new_root
    while not node.leaf:
      i = node.size - 1
      while i > 0 and payload < node.keys[i] :
        i -= 1
      if payload > node.keys[i]:
        i += 1

      next = node.children[i]
      if next._is_full:
        node = next.split(node, payload)
      else:
        node = next
    node.add_key(payload)

  def search(self, value, node=None):
    if node is None:
      node = self.root
    if value in node.keys:
      return True
    elif node.leaf:
      return False
    else:
      i = 0
      while i < node.size and value > node.keys[i]:
        i += 1
      return self.search(value, node.children[i])
    
  def inorder(self):
      print("Thu tu cac key tang dan")
      node = self.root
      this_level = [self.root]
      res = []
      while this_level:
        next_level = []
        for node in this_level:
            if node.children:
                next_level.extend(node.children)
            for n in node.keys:
                res.append(n)
        this_level = next_level
      res.sort()
      print(res)
      
  def dequy(self,node,k,output):
      if k >0:
        n_chenh = len(output[k*3]) - len(output[k*3-1])
        output[k*3-1] += " " * (n_chenh)
        output[k*3-2] += " " * (n_chenh)
      
      output[k*3] = output[k*3] + 6*" "+ str(node.keys) 
      tmp = len(str(node.keys))
      if (k>0):
          output[k*3-1] = output[k*3-1] + 6*" "+  " |" +(tmp - 2)*" "
          output[k*3-2] = output[k*3-2] + 6*" "+ " |" +(tmp - 2)*" "
    
          while (len(output[k*3-3]) < len(output[k*3])-2):
                output[k*3-3] =  output[k*3-3] + "-" 
      if not node.leaf:
          for sub_node  in node.children:
              self.dequy(sub_node,k+1,output)
             
  def n_level(self):
      n = 0
      this_level = [self.root]
      while this_level:
          n = n + 1
          next_level = []
          output = ""
          for node in this_level:
            if node.children:
              next_level.extend(node.children)
            output += str(node.keys) + " "
          this_level = next_level      
      return n    
      
  def print_tree(self):
      output = []
      node = self.root
      level = self.n_level()
      for i in range(level+2):
          output.append(" ")
          output.append(" ")
          output.append(" ")
      self.dequy(node,0,output)
      for i in range(level+1):
          while (output[i*3][len(output[i*3])-1] == "-" and output[i*3+1][len(output[i*3])-1] == " "):
              output[i*3] = output[i*3][:-1]
      print("cau truc cay:")          
      for i in range(level*3):
          print(output[i])
  
      
  def print_order(self):
    this_level = [self.root]
    while this_level:
      next_level = []
      output = ""
      for node in this_level:
        if node.children:
          next_level.extend(node.children)
        output += str(node.keys) + " "
      print(output)
      this_level = next_level      
## test      
bTree = BTree(2)
## them node vao cay
for i in range(1,20):
    bTree.insert(i*2)
# in ra thu tu key tang dan    
bTree.inorder()
# in ra cay
bTree.print_tree()
