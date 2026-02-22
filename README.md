# Due-Diligence
Boolean Chart
ketching a solutionI'm working through implementing an ANSI-style gate symbol graphic with Graphviz, aiming to simulate an OR gate symbol using 'Mrecord' and a custom label.Coding and executingPythonfrom graphviz import Digraph

# ANSI-style (approximate) logic-gate schematic for the same Boolean rule
# KNOWN = PM ∨ 811 ∨ GIS ∨ (CUT ∨ DEAD ∨ HOT)
# Graphviz doesn't ship true ANSI gate glyphs, so we emulate with shaped nodes and labels.

G = Digraph('ANSI_Gates', format='png')
G.attr(rankdir='LR', splines='ortho')

# Inputs as ANSI-style input ports (circles)
for n,label in [('PM','PM'),('OC','811'),('GIS','GIS'),('CUT','CUT'),('DEAD','DEAD'),('HOT','HOT')]:
    G.node(n, label, shape='circle', width='0.6', height='0.6', fontsize='12')

# Visual OR gate (3-input OR)
G.node('OR_VIS', '{ {CUT|DEAD|HOT} | OR }', shape='Mrecord', style='rounded', fontsize='12')

# Upper ORs: (PM OR 811) -> OR1; (GIS OR VIS) -> OR2
G.node('OR1', '{ {PM|811} | OR }', shape='Mrecord', style='rounded', fontsize='12')
G.node('OR2', '{ {GIS|VIS} | OR }', shape='Mrecord', style='rounded', fontsize='12')

# Top OR
G.node('OR_TOP', '{ {OR1|OR2} | OR }', shape='Mrecord', style='rounded,bold', fontsize='12')

# Output lamp (doublecircle)
G.node('KNOWN', 'KNOWN', shape='doublecircle', width='0.7', height='0.7', fontsize='12')

# Wiring (approximate ANSI layout using orthogonal edges)
# Inputs to OR_VIS
G.edge('CUT', 'OR_VIS:w')
G.edge('DEAD', 'OR_VIS:w')
G.edge('HOT', 'OR_VIS:w')

# Inputs to OR1
G.edge('PM', 'OR1:w')
G.edge('OC', 'OR1:w')

# Inputs to OR2 (GIS and VIS)
G.edge('GIS', 'OR2:w')
G.edge('OR_VIS:e', 'OR2:w')

# To OR_TOP
G.edge('OR1:e', 'OR_TOP:w')
G.edge('OR2:e', 'OR_TOP:w')

# To output
G.edge('OR_TOP:e', 'KNOWN:w')

# Legend cluster
with G.subgraph(name='cluster_legend') as c:
    c.attr(label='Legend (ANSI-style, emulated)', style='dashed', fontsize='12')
    c.node('L1', 'OR = ≥1 input true → output true', shape='note', fontsize='11')
    c.node('L2', 'KNOWN = proceed as “utility known/located”', shape='note', fontsize='11')

path = G.render('unknown_utility_ansi_or_gates')
path
Status: Success

Result: unknown_utility_ansi_or_gates.png
