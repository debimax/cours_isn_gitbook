# Source image vecteur

Dans l'exemple précédent utilisant une couche `ol.layer.Vector`, vous pouvez voir que les objets géographiques ont été recalculés en permanence pendant le zoom animé (la taille des symboles de points reste fixe).  Avec une couche vecteur, OpenLayers refera le rendu de la source avec chaque frame de l'animation.  Cela fournit un rendu consistant des traits de ligne, des symboles de points  et des étiquettes avec les changements de résolution de la vue.

Une stratégie alternative de rendu est d'éviter de refaire le rendu pendant les transitions de la vue et à la place de repositionner et mettre à l'échelle la sortie depuis l'état de la vue précédente.  Cela peut être accompli en utilisant une `ol.layer.Image` avec une `ol.source.ImageVector`.  Avec cette combinaison, des "snapshots" de votre donnée sont rendus quand la vue n'est pas animée, et ces snapshots sont réutilisés pendant les transitions de vue.

L'exemple ci-dessous utilise une `ol.layer.Image` avec une `ol.source.ImageVector`.  Bien que cet exemple fasse seulement un rendu d'une petite quantité de données, cette combinaison serait plus appropriée pour les applications qui font une rendu de grandes quantités de données relativement statiques.

## `ol.source.ImageVector`

Revenons à l'exemple de couche vecteur pour avoir les données des tremblements de terre au dessus d'une carte du monde.

```html
<html>  
<head>  
<meta charset="utf-8" />  
<script type="text/javascript" src="https://www.brython.info/src/brython.js"></script>  
<script type="text/javascript" src="https://openlayers.org/en/v4.1.1/build/ol.js"></script>  
<link rel="stylesheet" type="text/css" href="https://openlayers.org/en/v4.1.1/css/ol.css">  
<style>  
#map {  
height: 400px;  
width: 100%;  
}  
.ol-attribution a {  
color: black;  
}  
</style>  
</head>  
<body onload="brython(1)">  
<div id="map" class ="map"> </div>  
  
<div id="info"></div>  
  
<script type="text/python">  
from browser import window, document  
ol = window.ol  
  
tremblement_terre =  ol.layer.Vector.new({  
      'title': 'Earthquakes',  
      'source':  ol.source.Vector.new({  
        'url': 'https://raw.githubusercontent.com/boundlessgeo/ol3-workshop/master/src/data/layers/7day-M2.5.json',  
        'format':  ol.format.GeoJSON.new()  
      }),  
      'style':  ol.style.Style.new({  
        'image':  ol.style.Circle.new({  
          'radius': 3,  
          'fill':  ol.style.Fill.new({'color': 'white'})  
        })  
     })  
  })  
  
map =  ol.Map.new({  
  'target': 'map',  
  'layers': [  
    ol.layer.Tile.new({  
      'title': 'Global Imagery',  
      'source':  ol.source.TileWMS.new({  
        'url': 'https://ahocevar.com/geoserver/wms',  
        'params': {'LAYERS': 'nasa:bluemarble', 'TILED': True}  
      })  
    }),  
tremblements_terre  
  ],  
  'view':  ol.View.new({  
    'projection': 'EPSG:4326',  
    'center': [5.7626, 45.1734],  
    'zoom': 1,  
    'maxResolution': 0.703125  
})  
})  

def get_feature(e):    
    ft = map.forEachFeatureAtPixel(e.pixel, lambda feature, layer: feature)  
    info_element = document['info']  
    #info_element.text = ft.getKeys()  
    try:  
        info_element.text = ft.get('title')  
    except:  
        info_element.text = ""  
  
map.on('singleclick', get_feature)   
</script>  
</body>  
</html>  
```

### Tâches

1. Ouvrez `map.html` dans votre éditeur de texte et copiez dedans le contenu de l'exemple vecteur ci-dessus. Sauvez vos changements et confirmez que les choses fonctionnent bien dans votre navigateur: {{ book.workshopUrl }}/map.html

1. Changez la couche vecteur tremblements_terre en:

  ```python
tremblements_terre =  ol.layer.Image.new({
  'title': 'Earthquakes',
  'source':  ol.source.ImageVector.new({
    'source':  ol.source.Vector.new({
      'url': '7day-M2.5.json',
      'format':  ol.format.GeoJSON.new()
    }),
    'style':  ol.style.Style.new({
      'image':  ol.style.Circle.new({
        'radius': 3,
        'fill':  ol.style.Fill.new({'color': 'white'})
      })
    })
  })
})
  ```

1. Rechargez {{ book.workshopUrl }}/map.html dans votre navigateur

  *Note* - Vous verrez la même donnée vecteur mais représentée comme une image. Cela rendra toujours possible des choses comme la détection des objets géographiques, mais la donnée vecteur sera moins tranchée. En fait, il s'agit d'un compromis entre performance et qualité.

### Examen de détails

Examinons la création de la couche pour avoir une idée de ce qui se passe.

```python
tremblements_terre = ol.layer.Image.new({
  'title': 'Earthquakes',
  'source': ol.source.ImageVector.new({
    'source': ol.source.Vector.new({
      'url': '7day-M2.5.json',
      'format': ol.format.GeoJSON.new()
    }),
    'style': ol.style.Style.new({
      'image': ol.style.Circle.new({
        'radius': 3,
        'fill': ol.style.Fill.new({'color': 'white'})
      })
    })
  })
})
```

Nous utilisons une `ol.layer.Image` au lieu d'une `ol.layer.Vector`. Cependant, nous pouvons toujours utiliser les données vecteur ici via `ol.source.ImageVector` qui est connectée à notre source originale vecteur `ol.source.Vector`. Le style est fourni comme une configuration de `ol.source.ImageVector` et pas sur la couche.

### Tâches bonus

1. Vérifiez que la détection d'objets géographiques fonctionne toujours en assignant un événement de type `'singleclick'` sur votre carte qui appelle `forEachFeatureAtPixel` sur la carte et affichez les informations de tremblements de terre en dessous de la fenêtre d'affichage de carte.
