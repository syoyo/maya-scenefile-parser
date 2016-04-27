### Autodesk Maya Scenefile Parser

Parse Binary and Ascii Maya scene files with Python.

Supports Maya 8.5-2017.

**Alternatives**

- [cgkit](https://github.com/yamins81/cgkit/blob/master/cgkit/mayabinary.py)
- [westernx/mayatools](https://github.com/westernx/mayatools/blob/master/mayatools/binary.py)


<br>
<br>
<br>

### Usage

Parsing either an `.ma` of `.mb` involves subclassing a parser superclass and implementing various handles, such as when a node or attribute creation event takes place.

```python
from maya_scenefile_parser import MayaAsciiParser, MayaBinaryParser

nodes = dict()

ext = os.path.splitext(path)[1]
try:
    Base, mode = {
        ".ma": (MayaAsciiParser, "r"),
        ".mb": (MayaBinaryParser, "rb"),
    }[ext]
except KeyError:
    raise RuntimeError("Invalid maya file: %s" % path)

class Parser(Base):
    def on_create_node(self, nodetype, name, parent):
        self.current_node = (name, parent, nodetype)

    def on_set_attr(self, name, value, type):
        if name not in ("nts", "notes"):
            return

        if self.current_node not in nodes:
            nodes[self.current_node] = {}

        nodes[self.current_node][name] = value

        print("{name} = {value} ({type})".format(**locals()))

with open(path, mode) as f:
    parser = Parser(f)
    parser.parse()

for node, attrs in nodes.iteritems():
    for key, value in attrs.iteritems():
        print("{node}.{key} = {value}".format(**locals()))

```
