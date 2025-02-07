<html lang="en">
    <!-- 
    A sample framework for the ESA-NASA WebWorldWind web applications.
    Author: Bruce Schubert
    License: MIT
    See: https://worldwind.arc.nasa.gov/web/ 
    -->
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <meta name="description" content="ESA-NASA WebWorldWind application framework with Bootstrap and KnockoutJS by Bruce Schubert">
        <meta name="author" content="Bruce Schubert">
        
        <title>WebWorldWind Example App</title>

        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootswatch/3.3.7/slate/bootstrap.min.css" type="text/css" />
        <style>
            body {
                padding-top: 50px;
                /* Move the content down because we have a fixed navbar that is 50px tall */
            }
            @media (min-width: 768px) {
                .sidebar-left {
                    position: fixed;
                    top: 51px;
                    bottom: 0;
                    left: 0;
                    z-index: 1000;
                    display: block;
                    padding-top: 0px;
                    padding-left: 20px;
                    padding-right: 20px;
                    padding-bottom: 20px;
                    overflow-x: hidden;
                    overflow-y: auto;
                    border-right: 1px solid #eee;
                }
            }
            @media (max-width: 767px) {
                .section-heading .section-toggle:after {
                    font-family: 'Glyphicons Halflings';
                    content: "\e114";
                    float: none;
                    color: lightgrey;
                    /* symbol for opening sections */
                }
                .section-heading .section-toggle.collapsed:after {
                    content: "\e080";
                    /* symbol for closing sections */
                }
                a.section-toggle  {
                    text-decoration: none;
                }
            }

            .main {
                padding: 0px;
                padding-left: 15px;
                padding-right: 15px;
                padding-bottom: 15px;
            }
            @media (min-width: 768px) {
                padding-left:20px;
                padding-right:20px;
            }
            .section-heading {
            }
            .sub-header {
                padding-bottom: 10px;
                border-bottom: 1px solid #eee;
                cursor: default;
                -webkit-user-select: none;  /* Chrome all / Safari all */
                -moz-user-select: none;     /* Firefox all */
                -ms-user-select: none;      /* IE 10+ */
            }
            .section-body>ul {
                padding-left: 0px;
            }
            /* Controls the style of enabled and disabled layers */
            .list-group-item {
                font-style: italic;
            }
            .list-group-item.active {
                font-style: normal;
            }  
        </style>
    </head>

    <body>
        <!-- Main Menu NavBar -->
        <nav class="navbar navbar-default navbar-fixed-top">
            <div class="container-fluid">
                <!-- Branding and 'hamburger' menu -->
                <div class="navbar-header">
                    <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#main-navbar" aria-expanded="false">
                        <span class="sr-only">Toggle navigation</span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                        <span class="icon-bar"></span>
                    </button>
                    <a class="navbar-brand" href="https://worldwind.arc.nasa.gov/web" target="_blank">ESA-NASA Web WorldWind</a>
                </div>
                <!-- Navigation links, Projections and Search Box -->
                <div class="collapse navbar-collapse" id="main-navbar">
                    <ul class="nav navbar-nav" role="tablist">
                        <!-- Home -->
                        <li class="active">
                            <a href="#home" role="tab" data-toggle="tab">
                                <span class="glyphicon glyphicon-home visible-sm-block" aria-hidden="true" style="padding-top:4px; padding-bottom:4px"></span>
                                <span class="hidden-sm" aria-hidden="true">Home</span>
                            </a>
                        </li>
                        <!-- Layers -->
                        <li>
                            <a href="#layers" role="tab" data-toggle="tab">
                                <span class="glyphicon glyphicon-list visible-sm-block" aria-hidden="true" style="padding-top:4px; padding-bottom:4px"></span>
                                <span class="hidden-sm" aria-hidden="true">Layers</span>
                            </a>
                        </li>
                        <!-- Markers -->
                        <li>
                            <a href="#markers" role="tab" data-toggle="tab">
                                <span class="glyphicon glyphicon-map-marker visible-sm-block" aria-hidden="true" style="padding-top:4px; padding-bottom:4px"></span>
                                <span class="hidden-sm" aria-hidden="true">Markers</span>
                            </a>
                        </li>
                    </ul>
                    <!-- Search Box -->
                    <div>
                        <form id="search" class="navbar-form navbar-left" role="search">
                            <div class="form-group">
                                <input type="text" class="form-control" placeholder="Destination" id="searchText" data-bind="value: searchText, valueUpdate: 'keyup', event: {keypress: onEnter}" />
                            </div>
                            <button id="searchButton" class="btn btn-primary" type="submit" data-bind="click: $root.performSearch">Go To</button>
                        </form>
                    </div>
                    <ul class="nav navbar-nav navbar-right">
                        <!-- Projections dropdown -->
                        <li id="projections" class="dropdown">
                            <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false"><span data-bind="text: currentProjection"></span> <span class="caret"></span></a>
                            <!-- Bind the list to the 'projections' observableArray -->
                            <ul id="projections-menu" class="dropdown-menu" data-bind="foreach: $root.projections">
                                <li data-bind="click: $root.changeProjection">
                                    <a><span data-bind="text: $data"></span></a>
                                </li>
                            </ul>
                        </li>
                    </ul>
                </div>
                <!-- /.navbar-collapse -->
            </div>
            <!-- /.container-fluid -->
        </nav>
        <!-- /NavBar -->

        <!-- Content -->
        <div class="container-fluid">
            <div class="row">
                <!-- Sidebar -->
                <div id="left-sidebar" class="col-sm-4 col-md-3 sidebar-left tab-content">

                    <!-- Home tab -->
                    <div id="home" class="tab-pane active" role="tabpanel">
                        <div class="section-heading">
                            <h4 class="sub-header">
                                <span class="glyphicon glyphicon-home" aria-hidden="true" style="padding-right:5px;"></span>
                                Home 
                                <a class="section-toggle" data-toggle="collapse" href="#home-body"></a>
                            </h4>
                        </div>
                        <div id="home-body" class="section-body collapse in">
                            <p>This is a 3D virtual globe application built from <a href="https://worldwind.arc.nasa.gov/web">ESA-NASA Web WorldWind</a> and the <a href="https://getbootstrap.com/docs/3.3/">Bootstrap</a> and <a href="http://knockoutjs.com/">KnockoutJS</a> libraries.</p>
                            <p>It features a 3D globe and 2D map projections, layer management, markers and a place name finder.
                            </p>
                        </div>
                    </div>
                    <!-- /Home -->

                    <!-- Layers tab -->
                    <div id="layers" class="tab-pane" role="tabpanel">
                        <div class="section-heading">
                            <h4 class="sub-header">
                                <span class="glyphicon glyphicon-list" aria-hidden="true" style="padding-right:5px;"></span>
                                Layers 
                                <a class="section-toggle" data-toggle="collapse" href="#layers-body">
                                </a>
                            </h4>
                        </div>
                        <div id="layers-body" class="section-body collapse in">
                            <!-- layer buttons bound to 'layers' observableArray -->
                            <!-- the button's active class is bound to 'layerEnabled' observable -->
                            <div class="list-group" data-bind="foreach: layers">
                                <button class="list-group-item btn btn-block" data-bind="click: $root.toggleLayer, css: {active: $data.layerEnabled}">
                                    <span data-bind="text: $data.displayName"></span>
                                </button>
                            </div>
                        </div>
                    </div>
                    <!-- /Layer -->

                    <!-- Markers tab -->
                    <div id="markers" class="tab-pane" role="tabpanel">
                        <div class="section-heading">
                            <h4 class="sub-header">
                                <span class="glyphicon glyphicon-map-marker" aria-hidden="true" 
                                      style="padding-right:5px;"></span>
                                Markers
                                <a class="section-toggle" data-toggle="collapse" href="#markers-body"></a>
                            </h4>
                        </div>
                        <div id="markers-body" class="section-body collapse in">
                            <!-- Bind the list to the 'markers' observableArray -->
                            <ul id="markers-list" data-bind="foreach: $root.markers">
                                <li class="btn-group btn-block btn-group-sm">
                                    <!-- Goto Button -->
                                    <button type="button" class="btn btn-default" data-bind="click: $root.gotoMarker">
                                        <span><img width="16px" height="16px" data-bind="attr:{src: $data.attributes.imageSource}" />  </span>
                                        <span data-bind="text: $data.label"></span>
                                    </button>
                                    <!-- Edit Button -->
                                    <button type="button" class="btn btn-default glyphicon glyphicon-pencil" data-bind="click: $root.editMarker"></button>
                                    <!-- Delete Button -->
                                    <button type="button" class="btn btn-default glyphicon glyphicon-trash" data-bind="click: $root.removeMarker"></button>
                                </li>
                            </ul>
                        </div>
                    </div>
                    <!-- /Markers -->
                </div>
                <!-- /Sidebar -->

                <!-- Main Content -->
                <div id="globe" class="col-sm-8 col-sm-offset-4 col-md-9 col-md-offset-3 main" style="height: calc(100vh - 100px);">
                    <div class="row" style="height: 100%;">
                        <!-- Globe -->
                        <div class="col-md-12" style="height: 100%;">
                            <div class="section-heading" style="width: 100%">
                                <h4 class="sub-header">
                                    <span class="glyphicon glyphicon-globe" aria-hidden="true" style="padding-right:5px;"></span>
                                    Globe
                                    <!-- Add Marker button and palette -->
                                    <span class="btn-group" style="float: right">
                                        <button type="button" 
                                                class="btn btn-primary btn-sm"
                                                style="padding-top: 0; padding-bottom: 0;"
                                                data-bind="click: $root.addMarker">
                                            <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                                            <img width="28px" height="28px;" 
                                                 data-bind="attr:{src: $root.selectedMarkerImage}"/>
                                        </button>
                                        <button type="button" 
                                                class="btn btn-primary btn-sm dropdown-toggle" 
                                                data-toggle="dropdown" 
                                                aria-haspopup="true" aria-expanded="false">
                                            <span class="caret"></span>
                                            <span class="sr-only">Markers Dropdown</span>
                                        </button>
                                        <!-- Bind the list to the 'markerPalette' observableArray -->
                                        <ul id="marker-palette" 
                                            class="dropdown-menu" 
                                            style="left: initial; right: 0; min-width: 30px"
                                            data-bind="foreach: markerPalette">
                                            <li data-bind="click: $root.selectedMarkerImage">
                                                <a><img width="28px" height="28px;" data-bind="attr:{src: $data}"/></a>
                                            </li>
                                        </ul>
                                    </span>
                                </h4>
                            </div>
                            <!-- NASA Web World Wind -->
                            <canvas id="canvasOne" style="width: 100%; height: 100%; 
                                    background-color: rgb(36,74,101); 
                                    border:1px solid #000000;" data-bind="style: { cursor: dropIsArmed() ? 'crosshair' : 'default' }">
                                Try Chrome or FireFox.
                            </canvas>
                            <!-- NASA Web World Wind -->
                        </div>
                    </div>
                </div>
            </div>
        </div>        
        <!-- Edit Marker Modal -->
        <div class="modal fade" id="editMarkerModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
            <div class="modal-dialog" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal" aria-label="Close"><span aria-hidden="true">&times;</span>
                        </button>
                        <h4 class="modal-title" id="myModalLabel">Edit Marker</h4>
                    </div>
                    <div class="modal-body">
                        <div class="input-group">
                            <!--             <span class="input-group-addon" id="name-addon">Name</span>
                                        <input type="text" class="form-control" id="marker-name" aria-describedby="name-addon">
                            -->          
                        </div>          
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-default" data-dismiss="modal">Close</button>
                        <button type="button" class="btn btn-primary">Save changes</button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Libraries -->
        <script src="https://code.jquery.com/jquery.min.js"></script>
        <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/knockout/3.4.2/knockout-min.js"></script>
        <script src="https://files.worldwind.arc.nasa.gov/artifactory/web/0.9.0/worldwind.min.js"></script>

        <!-- Custom auto-expand/collapse behaviors -->
        <script language="javascript">
            // Auto-expand section-bodies when not small
            $(window).resize(function () {
                if ($(window).width() >= 768) {
                    $('.section-body').collapse('show');
                }
            });
            // Auto-collapse navbar when tab items are clicked
            $('.navbar-collapse a[role="tab"]').click(function () {
                $(".navbar-collapse").collapse('hide');
            });
            // Auto-scroll-into-view expanded dropdown menus
            $('.dropdown').on('shown.bs.dropdown', function (event) {
                event.target.scrollIntoView(false); // align to bottom   
            });

            $(document).ready(function () {
                "use strict";

                WorldWind.Logger.setLoggingLevel(WorldWind.Logger.LEVEL_WARNING);

                // ----------------
                // Setup the globe
                // ----------------

                // Attach the WorldWindow globe to the HTML5 canvas 
                var wwd = new WorldWind.WorldWindow("canvasOne"),
                    MARKERS = "Markers",
                    markersViewModel,
                    globeViewModel;


                /**
                 * Returns the first layer with the given name.
                 * @param {String} name
                 * @returns {WorldWind.Layer}
                 */
                function findLayerByName(name) {
                    var layers = wwd.layers.filter(function (layer) {
                        return layer.displayName === MARKERS;
                    });
                    return layers[0];
                }


                // ------------------------------------
                // Define a view model for the globe
                // ------------------------------------
                function GlobeViewModel(wwd, markersViewModel) {
                    var self = this;

                    // Marker icons used in the marker palette 
                    self.markerPalette = ko.observableArray([
                        "https://files.worldwind.arc.nasa.gov/artifactory/web/0.9.0/images/pushpins/castshadow-red.png",
                        "https://files.worldwind.arc.nasa.gov/artifactory/web/0.9.0/images/pushpins/castshadow-green.png",
                        "https://files.worldwind.arc.nasa.gov/artifactory/web/0.9.0/images/pushpins/castshadow-blue.png",
                        "https://files.worldwind.arc.nasa.gov/artifactory/web/0.9.0/images/pushpins/castshadow-orange.png",
                        "https://files.worldwind.arc.nasa.gov/artifactory/web/0.9.0/images/pushpins/castshadow-teal.png",
                        "https://files.worldwind.arc.nasa.gov/artifactory/web/0.9.0/images/pushpins/castshadow-purple.png",
                        "https://files.worldwind.arc.nasa.gov/artifactory/web/0.9.0/images/pushpins/castshadow-white.png",
                        "https://files.worldwind.arc.nasa.gov/artifactory/web/0.9.0/images/pushpins/castshadow-black.png"
                    ]);
                    // The currently selected marker icon 
                    self.selectedMarkerImage = ko.observable(self.markerPalette()[0]);
                    // Used for cursor style and click handling 
                    self.dropIsArmed = ko.observable(false);
                    //The dropCallback is supplied with the click position and the dropObject.
                    self.dropCallback = null;
                    // The object passed to the dropCallback 
                    self.dropObject = null;

                    /**
                     * Adds a marker to the globe.
                     */
                    self.addMarker = function () {
                        self.dropIsArmed(true);
                        self.dropCallback = markersViewModel.dropMarkerCallback;
                        self.dropObject = self.selectedMarkerImage();
                    };

                    // Invoke addMarker when an image is selected from the palette
                    self.selectedMarkerImage.subscribe(self.addMarker);

                    /**
                     * Handles a click on the WorldWindow. If a "drop" action callback has been
                     * defined, it invokes the function with the picked location.
                     * @param {Object} event 
                     */
                    self.handleClick = function (event) {
                        if (!self.dropIsArmed()) {
                            return;
                        }
                        var type = event.type,
                            x, y,
                            pickList,
                            terrain;
                        // Get the clicked window coords
                        switch (type) {
                            case 'click':
                                x = event.clientX;
                                y = event.clientY;
                                break;
                            case 'touchend':
                                if (!event.changedTouches[0]) {
                                    return;
                                }
                                x = event.changedTouches[0].clientX;
                                y = event.changedTouches[0].clientY;
                                break;
                        }
                        if (self.dropCallback) {
                            // Get all the picked items 
                            pickList = wwd.pickTerrain(wwd.canvasCoordinates(x, y));
                            // Terrain should be one of the items if the globe was clicked
                            terrain = pickList.terrainObject();
                            if (terrain) {
                                self.dropCallback(terrain.position, self.dropObject);
                            }
                        }
                        self.dropIsArmed(false);
                        event.stopImmediatePropagation();
                    };

                    // Assign the click handler to the WorldWind
                    wwd.addEventListener('click', function (event) {
                        self.handleClick(event);
                    });
                }


                // ------------------------------------
                // Define a view model for the layers
                // ------------------------------------
                function LayersViewModel(wwd) {
                    var self = this;
                    // WMS configurations for USGS WMS layers
                    // See: https://basemap.nationalmap.gov/arcgis/rest/services/
                    // See: https://services.nationalmap.gov/arcgis/rest/services/
                    var usgsTopoCfg = {
                        title: "USGS Topo Basemap",
                        version: "1.3.0",
                        service: "https://basemap.nationalmap.gov/arcgis/services/USGSTopo/MapServer/WmsServer?",
                        layerNames: "0",
                        sector: new WorldWind.Sector(-89.0, 89.0, -180, 180),
                        levelZeroDelta: new WorldWind.Location(36, 36),
                        numLevels: 13, // capabilites says 16, but really 13. limit to prevent requests for blank tiles
                        format: "image/png",
                        size: 256,
                        coordinateSystem: "EPSG:4326", // optional
                        styleNames: "" // (optional) A comma separated list of the styles to include 
                    },
                        usgsImageryTopoCfg = {
                            title: "USGS Imagery Topo Basemap",
                            version: "1.3.0",
                            service: "https://basemap.nationalmap.gov/arcgis/services/USGSImageryTopo/MapServer/WmsServer?",
                            layerNames: "0",
                            sector: new WorldWind.Sector(-89.0, 89.0, -180, 180),
                            levelZeroDelta: new WorldWind.Location(36, 36),
                            numLevels: 12,
                            format: "image/png",
                            size: 256,
                            coordinateSystem: "EPSG:4326", // optional
                            styleNames: "" //A comma separated list of the styles
                        },
                        usgsContoursCfg = {
                            title: "USGS Contour Lines Overlay",
                            version: "1.3.0",
                            service: "https://services.nationalmap.gov/arcgis/services/Contours/MapServer/WmsServer?",
                            //layerNames: "1,2,4,5,7,8", // lines and labels: large scale, 50' and 100' respectively
                            layerNames: "10,11,12,14,15,16,18,19", // lines and labels: large scale, 50' and 100' respectively
                            sector: new WorldWind.Sector(18.915561901, 64.8750000000001, -160.544024274, -66.9502505149999),
                            levelZeroDelta: new WorldWind.Location(36, 36),
                            numLevels: 19,
                            format: "image/png",
                            size: 256,
                            coordinateSystem: "EPSG:4326", // optional
                            styleNames: "" //  A comma separated list of the styles
                        };
                    // Define our layers and layer options
                    var layer,
                        layerCfg = [{
                                layer: new WorldWind.BMNGLayer(),
                                enabled: true
                            }, {
                                layer: new WorldWind.BMNGLandsatLayer(),
                                enabled: true
                            }, {
                                layer: new WorldWind.WmsLayer(usgsImageryTopoCfg),
                                enabled: false,
                                detailControl: 2.0,
                                disableTransparentColor: true  // prevent white labels from being transparent
                            }, {
                                layer: new WorldWind.WmsLayer(usgsTopoCfg),
                                enabled: false,
                                disableTransparentColor: true  // prevent white labels from being transparent
                            }, {
                                layer: new WorldWind.BingAerialLayer(),
                                enabled: false
                            }, {
                                layer: new WorldWind.BingAerialWithLabelsLayer(),
                                enabled: true
                            }, {
                                layer: new WorldWind.BingRoadsLayer(),
                                enabled: false
                            }, {
                                layer: new WorldWind.WmsLayer(usgsContoursCfg),
                                enabled: false,
                                opacity: 0.85
                            }, {
                                layer: new WorldWind.RenderableLayer("Markers"),
                                enabled: true
                            }, {
                                layer: new WorldWind.CompassLayer(),
                                enabled: true
                            }, {
                                layer: new WorldWind.CoordinatesDisplayLayer(wwd),
                                enabled: true
                            }, {
                                layer: new WorldWind.ViewControlsLayer(wwd),
                                enabled: true,
                                // Override the default placement from bottom-left to top-left
                                // Leave room at the top for the Coordinates output
                                placement: new WorldWind.Offset(
                                    WorldWind.OFFSET_FRACTION, 0,
                                    WorldWind.OFFSET_FRACTION, 1),
                                alignment: new WorldWind.Offset(
                                    WorldWind.OFFSET_PIXELS, -10,
                                    WorldWind.OFFSET_INSET_PIXELS, -18)
                            }];

                    // Apply the layer options and add the layers to the globe
                    for (var i = 0; i < layerCfg.length; i++) {
                        layer = layerCfg[i].layer;
                        layer.enabled = layerCfg[i].enabled;
                        layer.opacity = layerCfg[i].opacity ? layerCfg[i].opacity : 1.0;
                        if (layerCfg[i].placement) {
                            layer.placement = layerCfg[i].placement;
                        }
                        if (layerCfg[i].alignment) {
                            layer.alignment = layerCfg[i].alignment;
                        }
                        if (layerCfg[i].disableTransparentColor) {
                            layer.urlBuilder.transparent = false;
                        }
                        if (layerCfg[i].detailControl) {
                            layer.detailControl = layerCfg[i].detailControl;
                        }
                        // Set the layer's view model properties
                        layer.layerEnabled = ko.observable(layer.enabled);
                        // Add the layer to the globe
                        wwd.addLayer(layer);
                    }

                    // The layers collection view model
                    self.layers = ko.observableArray(wwd.layers);

                    // Layer item click handler
                    self.toggleLayer = function (layer) {
                        layer.enabled = !layer.enabled;
                        layer.layerEnabled(layer.enabled); // view model
                        wwd.redraw();
                    };
                }

                // -----------------------------------------
                // Define a view model for the projections
                // -----------------------------------------
                function ProjectionsViewModel(wwd) {
                    var self = this;
                    self.projections = ko.observableArray([
                        "3D",
                        "Equirectangular",
                        "Mercator",
                        "North Polar",
                        "South Polar",
                        "North UPS",
                        "South UPS",
                        "North Gnomonic",
                        "South Gnomonic"
                    ]);
                    // Projection support vars
                    self.roundGlobe = wwd.globe;
                    self.flatGlobe = null;
                    // Track the current projection
                    self.currentProjection = ko.observable('3D');
                    // Projection click handler
                    self.changeProjection = function (projectionName) {
                        // Capture the selection
                        self.currentProjection(projectionName);
                        // Change the projection
                        if (projectionName === "3D") {
                            if (!self.roundGlobe) {
                                self.roundGlobe = new WorldWind.Globe(new WorldWind.EarthElevationModel());
                            }
                            if (wwd.globe !== self.roundGlobe) {
                                wwd.globe = self.roundGlobe;
                            }
                        } else {
                            if (!self.flatGlobe) {
                                self.flatGlobe = new WorldWind.Globe2D();
                            }
                            if (projectionName === "Equirectangular") {
                                self.flatGlobe.projection = new WorldWind.ProjectionEquirectangular();
                            } else if (projectionName === "Mercator") {
                                self.flatGlobe.projection = new WorldWind.ProjectionMercator();
                            } else if (projectionName === "North Polar") {
                                self.flatGlobe.projection = new WorldWind.ProjectionPolarEquidistant("North");
                            } else if (projectionName === "South Polar") {
                                self.flatGlobe.projection = new WorldWind.ProjectionPolarEquidistant("South");
                            } else if (projectionName === "North UPS") {
                                self.flatGlobe.projection = new WorldWind.ProjectionUPS("North");
                            } else if (projectionName === "South UPS") {
                                self.flatGlobe.projection = new WorldWind.ProjectionUPS("South");
                            } else if (projectionName === "North Gnomonic") {
                                self.flatGlobe.projection = new WorldWind.ProjectionGnomonic("North");
                            } else if (projectionName === "South Gnomonic") {
                                self.flatGlobe.projection = new WorldWind.ProjectionGnomonic("South");
                            }
                            if (wwd.globe !== self.flatGlobe) {
                                wwd.globe = self.flatGlobe;
                            }
                        }
                        wwd.redraw();
                    };
                }

                // ---------------------------------------
                // Define the view model for the SearchBox
                // ---------------------------------------
                function SearchViewModel(wwd) {
                    var self = this;
                    self.geocoder = new WorldWind.NominatimGeocoder();
                    self.goToAnimator = new WorldWind.GoToAnimator(wwd);
                    self.searchText = ko.observable('');
                    self.onEnter = function (data, event) {
                        if (event.keyCode === 13) {
                            self.performSearch();
                        }
                        return true;
                    };
                    self.performSearch = function () {
                        var queryString = self.searchText();
                        if (queryString) {
                            var latitude, longitude;
                            if (queryString.match(WorldWind.WWUtil.latLonRegex)) {
                                var tokens = queryString.split(",");
                                latitude = parseFloat(tokens[0]);
                                longitude = parseFloat(tokens[1]);
                                self.goToAnimator.goTo(new WorldWind.Location(latitude, longitude));
                            } else {
                                self.geocoder.lookup(queryString, function (geocoder, result) {
                                    if (result.length > 0) {
                                        latitude = parseFloat(result[0].lat);
                                        longitude = parseFloat(result[0].lon);
                                        self.goToAnimator.goTo(new WorldWind.Location(latitude, longitude));
                                    }
                                });
                            }
                        }
                    };
                }

                // -------------------------------------
                // Define a view model for the markers
                // -------------------------------------
                function MarkersViewModel(wwd) {
                    var self = this;

                    self.markers = ko.observableArray();

                    // Set up the common placemark attributes.
                    self.commonAttributes = new WorldWind.PlacemarkAttributes(null);
                    self.commonAttributes.imageScale = 1;
                    self.commonAttributes.imageOffset = new WorldWind.Offset(
                        WorldWind.OFFSET_FRACTION, 0.3,
                        WorldWind.OFFSET_FRACTION, 0.0);
                    self.commonAttributes.imageColor = WorldWind.Color.WHITE;
                    self.commonAttributes.labelAttributes.offset = new WorldWind.Offset(
                        WorldWind.OFFSET_FRACTION, 0.5,
                        WorldWind.OFFSET_FRACTION, 1.0);
                    self.commonAttributes.labelAttributes.color = WorldWind.Color.YELLOW;
                    self.commonAttributes.drawLeaderLine = true;
                    self.commonAttributes.leaderLineAttributes.outlineColor = WorldWind.Color.RED;

                    /**
                     * "Drop" action callback creates and adds a marker (WorldWind.Placemark) to the globe. 
                     * 
                     * @param {WorldWind.Location} position
                     * @param {type} imageSource
                     */
                    self.dropMarkerCallback = function (position, imageSource) {
                        var attributes = new WorldWind.PlacemarkAttributes(self.commonAttributes),
                            placemark = new WorldWind.Placemark(position, /*eyeDistanceScaling*/true, attributes),
                            markerLayer = findLayerByName(MARKERS);

                        // Set the placemark properties and  attributes
                        placemark.label = "Lat " + position.latitude.toPrecision(4).toString() + "\n" + "Lon " + position.longitude.toPrecision(5).toString();
                        placemark.altitudeMode = WorldWind.CLAMP_TO_GROUND;
                        placemark.eyeDistanceScalingThreshold = 2500000;
                        attributes.imageSource = imageSource;

                        // Add the placemark to the layer and to the observable array
                        markerLayer.addRenderable(placemark);
                        self.markers.push(placemark);
                    };

                    /** Animator used to programmatically move the globe to a marker */
                    self.goToAnimator = new WorldWind.GoToAnimator(wwd);

                    /** 
                     * "Goto" function centers the globe on the given marker.
                     * @param {WorldWind.Placemark} marker
                     */
                    self.gotoMarker = function (marker) {
                        self.goToAnimator.goTo(new WorldWind.Location(
                            marker.position.latitude,
                            marker.position.longitude));
                    };

                    /** 
                     * "Edit" function invokes a modal dialog to edit the marker attributes.
                     * @param {WorldWind.Placemark} marker 
                     */
                    self.editMarker = function (marker) {
                        // TODO bind marker to dialog, maybe create an individual marker view-model
//                        var options = {};
//                        $('#editMarkerModal').modal(options)
                    };

                    /** 
                     * "Remove" function removes a marker from the globe.
                     * @param {WorldWind.Placemark} marker 
                     */
                    self.removeMarker = function (marker) {
                        var markerLayer = findLayerByName(MARKERS),
                            i, max, placemark;

                        // Find and remove the marker from the layer and the observable array
                        for (i = 0, max = self.markers().length; i < max; i++) {
                            placemark = markerLayer.renderables[i];
                            if (placemark === marker) {
                                markerLayer.renderables.splice(i, 1);
                                self.markers.remove(marker);
                                break;
                            }
                        }
                    };

                }

                // --------------------------------------------------------
                // Bing the view models to the corresponding HTML elements
                // --------------------------------------------------------
                markersViewModel = new MarkersViewModel(wwd);
                globeViewModel = new GlobeViewModel(wwd, markersViewModel);

                ko.applyBindings(new LayersViewModel(wwd), document.getElementById('layers'));
                ko.applyBindings(new ProjectionsViewModel(wwd), document.getElementById('projections'));
                ko.applyBindings(new SearchViewModel(wwd), document.getElementById('search'));
                ko.applyBindings(markersViewModel, document.getElementById('markers'));
                ko.applyBindings(globeViewModel, document.getElementById('globe'));

            });
        </script>

    </body>

</html>

