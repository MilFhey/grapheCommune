
import networkx as nx
import matplotlib.pyplot as plt

# Votre graphe et les données associées
G = nx.DiGraph()
tableau = [
    ("Les Abymes", "Morne-à-l'Eau", 9),
    ("Les Abymes", "Moule", 22),
    ("Les Abymes", "Pointe-à-Pitre", 5),
    ("Les Abymes", "Sainte-Anne", 24),
    ("Anse-Bertrand", "Moule", 32),
    ("Anse-Bertrand", "Pointe-à-Pitre", 9),
    ("Baie-Mahault", "Lamentin", 7),
    ("Baie-Mahault", "Petit-Bourg", 10),
    ("Baie-Mahault", "Pointe-à-Pitre", 11),
    ("Baillif", "Basse-Terre", 4),
    ("Baillif", "Vieux-Habitants", 4),
    ("Basse-Terre", "Gourbeyre", 5),
    ("Basse-Terre", "Saint-Claude", 6),
    ("Bouillante", "Pointe-Noire", 16),
    ("Bouillante", "Vieux-Habitants", 11),
    ("Capesterre-Belle-Eau", "Goyave", 12),
    ("Capesterre-Belle-Eau", "Trois-Rivières", 13),
    ("Deshaies", "Pointe-Noire", 14),
    ("Deshaies", "Sainte-Rose", 17),
    ("Gosier", "Pointe-à-Pitre", 9),
    ("Gosier", "Sainte-Anne", 14),
    ("Gourbeyre", "Trois-Rivières", 8),
    ("Goyave", "Petit-Bourg", 8),
    ("Lamentin", "Sainte-Rose", 12),
    ("Morne-à-l'Eau", "Moule", 13),
    ("Morne-à-l'Eau", "Petit-Canal", 8),
    ("Moule", "Petit-Canal", 21),
    ("Moule", "Sainte-Anne", 29),
    ("Moule", "Saint-François", 14),
    ("Petit-Canal", "Port-Louis", 9),
    ("Sainte-Anne", "Saint-François", 15),
    ("Trois-Rivières", "Vieux-Fort", 9),
]

for commune1, commune2, distance in tableau:
    G.add_edge(commune1, commune2, weight=distance)
    G.add_edge(commune2, commune1, weight=distance)  # Ajout de l'arête inverse

# Affichage des nœuds et des arêtes du graphe
print("Nœuds du graphe :")
print(G.nodes())
print("\nArêtes du graphe :")
print(G.edges(data=True))

# Affichage d'une liste numérotée des villes
print("\nSélectionnez un point de départ en entrant le chiffre correspondant :")
for i, ville in enumerate(G.nodes(), 1):
    print(f"{i}. {ville}")

# Sélection du point de départ
point_depart_index = int(input("Votre point de départ : ")) - 1
point_depart = list(G.nodes())[point_depart_index]

# Affichage d'une liste numérotée des destinations
print("\nSélectionnez une destination en entrant le chiffre correspondant :")
for i, ville in enumerate(G.nodes(), 1):
    print(f"{i}. {ville}")

# Sélection des destinations (circuit)
destinations = []
while True:
    destination_index = int(input("Ajoutez une destination (0 pour terminer) : ")) - 1
    if destination_index == -1:
        break
    destination = list(G.nodes())[destination_index]
    destinations.append(destination)

# Calcul du circuit le plus court
if len(destinations) > 1:
    # Plusieurs destinations, utilisez le problème du voyageur de commerce pour trouver le circuit le plus court
    circuit_plus_court = nx.approximation.traveling_salesman_problem(G, nodes=[point_depart] + destinations, cycle=True)
else:
    # Une seule destination, utilisez Dijkstra pour trouver le chemin le plus court
    circuit_plus_court = nx.dijkstra_path(G, point_depart, destinations[0])

print("\nCircuit le plus court :", circuit_plus_court)

# Création d'un nouveau graphe contenant uniquement les nœuds et arêtes du circuit le plus court
G_circuit_plus_court = G.subgraph(circuit_plus_court)

# Affichage du graphe du circuit le plus court avec les directions des arêtes et les poids
edge_labels_circuit = {(commune1, commune2): distance for commune1, commune2, distance in tableau if (commune1, commune2) in G_circuit_plus_court.edges}
pos_circuit = nx.spring_layout(G_circuit_plus_court)  # Ajuster la disposition pour un meilleur affichage
nx.draw(G_circuit_plus_court, pos_circuit, with_labels=True, arrows=True)
nx.draw_networkx_edge_labels(G_circuit_plus_court, pos_circuit, edge_labels=edge_labels_circuit, font_color='red', font_size=8)
plt.title("Graphe du Circuit le plus court")
plt.show()
