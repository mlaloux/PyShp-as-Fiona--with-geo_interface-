PyShp as Fiona (with geo_interface)
===================================

PyShp as Fiona (with geo_interface)


One of the great advantages of **Fiona** (Sean Gillies) is its ability to quickly examine the contents of a shapefile as dictionaries or write a shapefile in the same way, thanks to  iterators:

    >>> import fiona   
    >>> f = fiona.open('point.shp')  
    >>> f.next()  
    {'geometry': {'type': 'Point', 'coordinates': (161821.09375, 79076.0703125)}, 'id': '0', 'properties': {u'DIP_DIR': 120, u'STRATI_TYP': 1, u'DIP': 30}}
    >>> f.next()['geometry']['coordinates']  
    (161485.09375, 79272.34375)  
    >>> f.next()['properties']['DIP']  
    55  



but now, with the __geo_interface__ for shapes of Christian Lederman, it is possible to do the same thing with **Pyshp**:

    def records(filename):  
        # iterator  
        reader = shapefile.Reader(filename)  
        fields = reader.fields[1:]  
        field_names = [field[0] for field in fields]  
        for sr in reader.shapeRecords():  
            geom = sr.shape.__geo_interface__  
            atr = dict(zip(field_names, sr.record))  
            yield dict(geometry=geom,properties=atr)    
        
with points shapefile: 

    >>> import shapefile
    >>> a = records('point.shp')
    >>> a.next()
    {'geometry': {'type': 'Point', 'coordinates': (161821.09375, 79076.0703125)}, 'properties': {'DIP_DIR': 120, 'STRATI_TYP': 1, 'DIP': 30}}
    >>> a.next()['geometry']['coordinates']
    (161485.09375, 79272.34375)
    >>> a.next()['properties']['DIP']
    55
    
with lines shapefile: 

    >>> b = record('line.shp')
    >>> b.next()
    {'geometry': {'type': 'LineString', 'coordinates': ((146891.65625, 88174.265625), (146951.25, 88174.265625))},    'properties': {'VAR_FORM': 'NEFA', ''CLASSE': 'VAR'', 'LENGTH': 59.59}}
    
with polygons shapefile:  

     
    >>> c= records('polygon.shp')
    >>> c.next()
    {'geometry': {'type': 'Polygon', 'coordinates': (((166500.64206965509, 114695.34460173383), (166480.3066551598, 114693.60541532337), (166468.3914291781, 114692.18187126353), (166464.39182593071, 114692.26803581366),(166500.64206965509, 114695.34460173383)),)}, 'properties': {'CLASSE': 'VAR', 'FORM': 'X','VAR_FORM': 'NEFA', 'SYMBOL': 65}}
    
with **shapely** (like with **pygeoif** of Christian Lederman):

    >>> from shapely.geometry import shape    
    >>> a = records('point.shp') 
    >>> print shape( a.next()['geometry'])
    POINT (160689.5000000000000000 78371.9218750000000000)
    >>> b = records('line.shp')
    >>> print shape( b.next()['geometry'])
    LINESTRING (146891.6562500000000000 88174.2656250000000000, 146951.2500000000000000 88174.2656250000000000)
    >>> c = records('polygon.shp')
    >>> print shape( c.next()['geometry'])
    POLYGON ((166500.6420696550922003 114695.3446017338283127, 166480.3066551598021761 114693.6054153233708348, 166468.3914291780965868 114692.1818712635285920, 166464.3918259307101835 114692.2680358136567520, 166500.6420696550922003 114695.3446017338283127))
