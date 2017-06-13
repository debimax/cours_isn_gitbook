# Modifier des objets géographiques

La modification des objets géographiques fonctionne en utilisant une `ol.interaction.Select` combinée avec une `ol.interaction.Modify`. Elles partagent une collection (`ol.Collection`) en commun d'objets géographiques. Les objets géographiques sélectionnés avec `ol.interaction.Select` deviennent des candidats aux modifications avec `ol.interaction.Modify`.

## Créer une couche vecteur et une interaction de type `Modify`

### Tâches

1. Commencez avec un exemple qui fonctionne.  Ouvrez `map.html` dans votre éditeur de texte et assurez vous qu'il ressemble à ce qui suit:

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

tremblements_terre = ol.layer.Vector.new({
  'title': 'Earthquakes',
  'source': ol.source.Vector.new({
    'url': 'https://raw.githubusercontent.com/boundlessgeo/ol3-workshop/master/src/data/layers/7day-M2.5.json',
    'format':  ol.format.GeoJSON.new()
  }),
  'style':  ol.style.Style.new({
    'image':  ol.style.Circle.new({
      'radius': 5,
      'fill':  ol.style.Fill.new({
        'color': '#0000FF'
      }),
      'stroke':  ol.style.Stroke.new({
        'color': '#000000'
      })
    })
  })
})

style =  ol.style.Style.new({
  'image':  ol.style.Circle.new({
    'radius': 7,
    'fill':  ol.style.Fill.new({
      'color': [0, 153, 255, 1]
    }),
    'stroke':  ol.style.Stroke.new({
      'color': [255, 255, 255, 0.75],
      'width': 1.5
    })
  }),
  'zIndex': 100000
});

select =  ol.interaction.Select.new({'style': style});

modify =  ol.interaction.Modify.new({
  'features': select.getFeatures()
});

map =  ol.Map.new({
  'interactions': ol.interaction.defaults().extend([select, modify]),
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
    'zoom': 2
  })
})
</script>
</body>
</html>
    ```

2.  Sauvez vos changements de `map.html` et ouvrez la page dans votre navigateur:  {{ book.workshopUrl }}/map.html. Pour voir la modification des objets géographiques en action, utilisez le clic souris pour sélectionner un tremblement de terre et ensuite bougez pour déplacer le point.

## Examen de détails

Examinons comment la modification des objets géographiques fonctionne.

```python
style =  ol.style.Style.new({
  'image':  ol.style.Circle.new({
    'radius': 7,
    'fill':  ol.style.Fill.new({
      'color': [0, 153, 255, 1]
    }),
    'stroke':  ol.style.Stroke.new({
      'color': [255, 255, 255, 0.75],
      'width': 1.5
    })
  }),
  'zIndex': 100000
});

select =  ol.interaction.Select.new({'style': style});

modify =  ol.interaction.Modify.new({
  'features': select.getFeatures()
});
```

Nous créons 2 interactions, une `ol.interaction.Select` pour sélectionner les objets géographiques avant de les modifier, et une `ol.interaction.Modify` pour modifier réellement les géométries. Elles partagent la même `ol.Collection` d'objets géographiques. Les objets géographiques sélectionnés en utilisant `ol.interaction.Modify` deviennent candidats à la modification avec `ol.interaction.Modify`. Comme précédemment, `ol.interaction.Select` est configurée avec une objet `style`, qui définit effectivement le style utilisé pour dessiner les objets géographiques sélectionnés. Quand un utilisateur clique sur la carte à nouveau, l'objet géographique est dessiné en utilisant le style de la couche.
