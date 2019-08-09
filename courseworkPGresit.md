---
id: litvis

narrative-schemas:
  - ../narrative-schemas/courseworkPG.yml

elm:
  dependencies:
    gicentre/elm-vegalite: latest
    gicentre/tidy: latest
---

@import "../css/datavis.less"

```elm {l=hidden}
import Tidy exposing (..)
import VegaLite exposing (..)
```

# Postgraduate Coursework

## Introduction

With the growth of usage for Internet of Things (IoT) devices in the world, the lack of security for them is the main issue. The vulnerability of IoT devices is major, and the purpose of this research is set to narrow/reduce them. In 2015 it was estimated that 5 to 9 billion connected IoT devices are available with a growth of 17% yearly [1]

{(questions|}

- How to utilize an Intrusion Detection System to analyse and detect the behaviour of real-time attacks on IoT devices
- What are the weakest protocols that are used to attack IoT devices.
- For countries which are in both of the topojson files (e.g China, US, Brazil, India), is the number of Telnet attacks related to the number of SSH attacks?

{|questions)}

The above research questions have helped me shape the upcoming visualisations as to assist me answer them.

## Data Resources

How IoT (Internet of Things) Devices are Infected, Attacks on IoT devices:
(https://securelist.com/it-threat-evolution-q2-2018-statistics/87170/)

{(visualization|}

In this section, I will insert my visualisation types I used to answer my research question, along with describing the intented actions. Will start by visualising the most protocols used to infect IoT devices through a pie chart, then use two geoMaps on the most vulnerable protocols which have been used to attack IoT devices by choosing the top 10 countries. Finally, start comapring the two most vulnerable protocols for repeated countries in a simple bubble chart.

### Pie Chart represents type of Protocols are most vulnerable

```elm {l=hidden}
birdData =
    toPolar
        [ ( "Telnet", 75.4 )
        , ( "SSH", 11.59 )
        , ( "HTTP", 5.53 )
        , ( "8291", 3.41 )
        , ( "8080", 2.57 )
        , ( "7547", 1.51 )
        , ( "Others", 1.5 )
        ]
```

```elm {v interactive}
pieExample : Spec
pieExample =
    let
        cfg =
            configure
                << configuration (coView [ vicoStroke Nothing ])
    in
    toVegaLite [ cfg [], layer [ pie birdData ] ]
```

<a />

```elm {l=hidden}
attackData =
    toPolar
        [ ( "SSH", 11.59 )
        , ( "HTTP", 5.53 )
        , ( "8291", 3.41 )
        , ( "8080", 2.57 )
        , ( "7547", 1.51 )
        , ( "Others", 1.5 )
        ]
```

```elm {v interactive}
pie2Example : Spec
pie2Example =
    let
        cfg =
            configure
                << configuration (coView [ vicoStroke Nothing ])
    in
    toVegaLite [ cfg [], layer [ pie attackData ] ]
```

_Left pie chart illustartes all protocls_ and _Right pie chart illustrates that Telnet protocol is excluded_

The Pie charts above shows similar extractions from the following [datasets](https://raw.githubusercontent.com/abbadi2019/DataVizResit/master/Pie_IoT.csv), where the left pie chart shows all vulnerable protocols with their percentages and Telnet protocol indicates that is the most vulnerable with 75% then SSH protocols is shown as the second highest. The remainder of the protocols are very small percentages which were very difficult to visualise, therefore I decided to exclude the Telnet protocol _(shown the right pie chart)_ to be able to visualise the rest of the protocols. This has enabled me to identify the difference in the less vulnerable protocols, e.g. port 7547 and others were very close and difficult to measure using visualising contact.

### GeoMap to represent IoT devices attacked via Telnet ...

<a href='https://postimages.org/' target='_blank'><img src='https://i.postimg.cc/bN3ZtVSM/Io-TDevices-Infectedvia-Telnetv2.gif' border='0' alt='Io-TDevices-Infectedvia-Telnetv2'/></a><a href='https://postimages.org/' target='_blank'><img src='https://i.postimg.cc/CZ2Y9XRt/Telnet-Scale.png' border='0' alt='Telnet-Scale'/></a>

The above geoMap is to visualise the most infected countries that use IoT devices via the most vulnerable protocols, the dataset is extracted from the following [csv](https://raw.githubusercontent.com/abbadi2019/DataVis/master/Telnet_IoT.csv) file. Even though the dataset is very limited but it has enabled us to visualise the top 10 infected countries using Telnet protocol. The tool used to creat the above visualising geoMap is from [visme.co](https:/visme.co) which enabled me to have a more interactive map to show how the relation of the country's population and infected IoT devices. This will be explained further in the justification section later.

### GeoMap to represent IoT devices attacked via SSH ...

<a href='https://postimages.org/' target='_blank'><img src='https://i.postimg.cc/VspFjsry/Io-TDevices-Infectedvia-SSHv2.gif' border='0' alt='Io-TDevices-Infectedvia-SSHv2'/></a><a href='https://postimages.org/' target='_blank'><img src='https://i.postimg.cc/vc6s63SL/SSH-Scale.png' border='0' alt='SSH-Scale'/></a>

The above geoMap is produced using [data table](https://raw.githubusercontent.com/abbadi2019/DataVis/master/SSH_IoT.csv) for the second most vulnerable protocol SSH, this was illustrated using the [visme.co](https:/visme.co) tool. Different dataset were used but this illustartes that certain countries are repeated for both protocols _(Telnet, SSH)_ , which indicates that these countries are targeted and have high usage of IoT devices compare to their population. A scale of colour indication is included in both of the geoMaps to indentify how the colours were used which will also be described in the justification section.

### Bubble Chart to Compare relationship for both protocols

```elm {v interactive}
logBubble : Spec
logBubble =
    let
        data =
            dataFromUrl "https://raw.githubusercontent.com/abbadi2019/DataVizResit/master/Comparison_SSH%26Telnet.csv"

        enc =
            encoding
                << position X
                    [ pName "SSH %"
                    , pMType Quantitative
                    , pScale [ scType scLog ]
                    , pAxis [ axGrid False ]
                    ]
                << position Y
                    [ pName "Telnet %"
                    , pMType Quantitative
                    , pScale [ scZero False ]
                    , pAxis [ axGrid False ]
                    ]
                << size
                    [ mName "Country"
                    , mMType Nominal
                    , mScale
                        [ scRange (raNums [ 2000, 300 ])
                        , scType scPow -- Area proportional to datum raised to power of some exponent
                        , scExponent 1.0 -- Default value (i.e. area directly proportional to datum)
                        ]
                    ]
    in
    toVegaLite [ width 500, height 300, data [], square [], enc [] ]
```

The above _bubbleChart_ is used to illustrate how the two main and vulnerable protocles are compared with repeated infected countries. Once the chart is visualised, it can be read instantly the goal behind its illustration. For instance, if we take a look at how countries such as China, India, Russia and South Korea have direct relationship between the two protocols that the numbers are increasing uphill along with their population. Whereas, USA IoT devices indicate that Telnet is less vulnerable than SSH which can also fall into the slope where the rest of the points fall under. There is a strange bubble that is plotted further than others and has no relationship to both protocols as Brazil have high infected IoT devices via Telnet and fewer via SSH.

{|visualization)}

{(designJustification|}

In this section, I will discuss how careful my designs were considered to have major imapct to readers. Will start by justfying the chosen colours, layout and how they can be interactive.

### Colour

Here, I will demonstrate how the colour coding are chosing. For the pie chart, used colours that are easy to visualise from the first glance. This is illustrated into two pie charts having to exclude one the main vulnerable protocols has enabled me to visulaise and compare the remainder of the protocols. For the GeoMap visuals, the tool have enabled to use darker colours for the countries with most infected IoT devices compared with their populations and the colour thickness started to reduce as the perecentage number of infected contries are recorded, this is illlustarted with colour scale. Finally, have started to comapre two main protocols and began to realise that the colour used (light blue) enables me to visualise the data instantly without confusion.

### Layout

The layout chosen is best visual to demonstrate the given datasets (even at its minimal). The reason behind choosing to layout the dataset as pie chart as its always visible when you have small data. Data were crumbled for most of the other protocols, therefore I decided to exclude one the of the main protocols to visiualise the data correctly. For the geoMaps, have been built clearly and are easyily read by externals. These were created using [visme.co](https:/visme.co) tool, and enabled me to use appropriate colour scale _(visually readable)_. Main layout used is bubble chart, where it allows to visiualise data instantly, clear and easy to read. For the bubble chart, we can seee that Brazil is off scope as it cannot fall on the slope line where the rest of the points are scattered and indicates that the data for his country is uncompareable protocols for the infected IoT devices related to their countries.

### Interactions

For this part, is to identify how the chosen visualisations interact for others to maniuplate and/or amend the datasets. Within the coursework, all my visuals are interactive, if a user hovers over the pie charts they will be able to identify which box or parameter is for each protocol. As for the geoMaps, used a tool to enable users to interact with hovering over each country to identify the infected IoT devices relatively to the population of that country. When built the geoMap I have recorded it as gif file to illustarte to the users _(if it was created using the code)_ how easy it is to visualise the data. Lastly, created a bubble chart that interacts correctly if hovered over the squared boxes and enable users to identify the data behind every scatter plot.

{|designJustification)}

{(validation|}

Further work I can introduce if this exercise was asked to be carried out again, is to identify how long it can take to detect attack on IoT devices using intrusion detection systems. Will produce the following Barchart:
<a href="https://ibb.co/rx5KtBw"><img src="https://i.ibb.co/T4PDTQg/Validation-Bar-Chart.jpg" alt="Validation-Bar-Chart" border="0"></a>
The bar chart illustratestates the number of minutes before each of the five attackers that were detected actually were detected. The average number of minutes before the attacks were detected was six hours and 41 minutes. The first attack that was detected was detected 5 hours and 36 minutes after the attack was introduced. The last attack that was detected was 7 hours and 41 minutes after the attack was introduced.

## Conclusion

Many takeways from the visualisations introduced in this coursework, as I identified the countries that are mostly infected using certain protocols by using Brut-force passwords approaches. The colour codes, layout and interaction are my main strengths but I have had few obstacles in creating the GeoMaps to code within Litviz.

{|validation)}

{(references|}

**Haroz, S., and Whitney, D.** (2012) How capacity limits of attention influence information visualization effectiveness. _IEEE Transactions on Visualization and Computer Graphics_, 18(12) pp.2402-2410.

[1] **Sheth, A.** (2016). Internet of Things to Smart IoT Through Semantic, Cognitive, and Perceptual Computing. _IEEE Intelligent Systems_, 31(2), pp.108-112.

**Borkin, M., Vo, A., Bylinskii, Z., Isola, P., Sunkavalli, S., Oliva, A. and Pfister, H.** (2013) [What makes a visualization memorable?](https://vcg.seas.harvard.edu/publications/what-makes-a-visualization-memorable/paper) _IEEE Transactions on Visualization and Computer Graphics_, 19(12), pp.2306-2315.

{|references)}

<!-- Appendix: Code for producing radial (pie) charts using an azimuthal map projection -->

```elm{l=hidden}
radialAxes : Spec
radialAxes =
    asSpec
        [ dataFromJson (geometry (graticule 30) []) []
        , projection [ prType azimuthalEquidistant, prRotate 0 90 0 ]
        , geoshape [ maStrokeWidth 0.2, maFilled False ]
        ]


pie : Spec -> Spec
pie =
    stack [ prType azimuthalEquidistant, prRotate 0 90 0 ]


donut : Spec -> Spec
donut geoData =
    let
        circleCoords lat =
            List.map (\x -> ( toFloat (x * 10), toFloat lat )) (List.range 0 36)

        pieSpec =
            asSpec
                [ dataFromJson geoData [ jsonProperty "features" ]
                , geoshape [ maStroke "white", maStrokeWidth 3 ]
                , encoding
                    (color
                        [ mName "properties.cat"
                        , mMType Nominal
                        , mLegend [ leTitle "" ]
                        ]
                        []
                    )
                ]

        blankSpec =
            asSpec
                [ dataFromJson (geometry (geoPolygon [ circleCoords 30 ]) []) []
                , geoshape [ maFill "#fff" ]
                ]
    in
    asSpec
        [ projection [ prType azimuthalEquidistant, prRotate 0 90 0 ]
        , layer [ pieSpec, blankSpec ]
        ]


rose : Spec -> Spec
rose geoData =
    let
        gratSpec =
            asSpec
                [ dataFromJson (geometry (graticule 22.5) []) []
                , geoshape [ maStrokeWidth 0.2, maFilled False ]
                ]

        roseSpec =
            asSpec
                [ dataFromJson geoData [ jsonProperty "features" ]
                , geoshape [ maFillOpacity 0.7, maStroke "#fff", maStrokeWidth 4 ]
                , encoding (color [ mStr "#ccc" ] [])
                ]
    in
    asSpec
        [ projection [ prType azimuthalEquidistant, prRotate 202 -90 0 ]
        , layer [ roseSpec, gratSpec ]
        ]


range : Float -> Float -> Float -> List Float
range mn mx step =
    List.range 0 ((mx - mn) / step |> round)
        |> List.map (\x -> mn + (toFloat x * step))


scanl : (a -> b -> b) -> b -> List a -> List b
scanl fn b =
    let
        scan a bs =
            case bs of
                hd :: tl ->
                    fn a hd :: bs

                _ ->
                    []
    in
    List.foldl scan [ b ] >> List.reverse


graticule : Float -> Geometry
graticule gStep =
    let
        meridian lng =
            List.map (\lat -> ( lng, lat )) (range (gStep - 90) (90 - gStep) (min 5 gStep))

        parallel lat =
            List.map (\lng -> ( lng, lat )) (range -180 180 (min 10 gStep))
    in
    geoLines
        (List.map parallel (range (gStep - 90) (90 - gStep) gStep)
            ++ List.map meridian (range -180 180 gStep)
        )


toPolar : List ( String, Float ) -> Spec
toPolar pairs =
    case pairs of
        [] ->
            -- Invisible polygon if no data provided.
            geoFeatureCollection [ geometry (geoPolygon [ [ ( 0, 90 ), ( 1, 90 ), ( 0, 90 ) ] ]) [ ( "cat", str "" ) ] ]

        [ ( label, _ ) ] ->
            -- Full circle if singleton provided.
            geoFeatureCollection
                [ geometry (geoPolygon [ [ ( 0, 90 ), ( 359.9, -89 ), ( 0, -89 ), ( 0, 90 ) ] ])
                    [ ( "cat", str label ) ]
                ]

        _ ->
            -- Segments if two or more data items provided.
            let
                geom ( a, b, label ) =
                    geometry (geoPolygon [ [ ( 0, 90 ), ( b, -89 ), ( a, -89 ), ( 0, 90 ) ] ])
                        [ ( "cat", str label ) ]

                adjacent xs =
                    List.map2 Tuple.pair xs (List.drop 1 xs ++ List.take 1 xs)

                triplet =
                    let
                        degs xs =
                            scanl (\a b -> 360 * a / List.sum xs + b) 0 xs
                    in
                    pairs
                        |> List.unzip
                        |> Tuple.second
                        |> degs
                        |> adjacent
                        |> List.map2 (\label ( a, b ) -> ( a, b, label )) (List.unzip pairs |> Tuple.first)
            in
            geoFeatureCollection (List.map geom triplet)


toRose : List ( String, Float ) -> Spec
toRose pairs =
    case pairs of
        [] ->
            -- Invisible polygon if no data provided.
            geoFeatureCollection [ geometry (geoPolygon [ [ ( 0, 90 ), ( 1, 90 ), ( 0, 90 ) ] ]) [ ( "cat", str "" ) ] ]

        [ ( label, _ ) ] ->
            -- Full circle if singleton provided.
            geoFeatureCollection
                [ geometry (geoPolygon [ [ ( 0, 90 ), ( 359.9, -89 ), ( 0, -89 ), ( 0, 90 ) ] ])
                    [ ( "cat", str label ) ]
                ]

        _ ->
            -- Segments if two or more data items provided.
            let
                geom ( ( a, b ), r, label ) =
                    geometry (geoPolygon [ [ ( 0, 90 ), ( b, r ), ( a, r ), ( 0, 90 ) ] ])
                        [ ( "cat", str label ) ]

                adjacent xs =
                    List.map2 Tuple.pair xs (List.drop 1 xs ++ List.take 1 xs)

                quad =
                    let
                        degs =
                            range 0 360 (360 / (List.length pairs |> toFloat))

                        maxR =
                            List.unzip pairs |> Tuple.second |> List.maximum |> Maybe.withDefault 1

                        rs =
                            List.unzip pairs |> Tuple.second |> List.reverse |> List.map (\r -> 80 - 169.9 * r / maxR)

                        labels =
                            List.unzip pairs |> Tuple.first |> List.reverse
                    in
                    degs |> adjacent |> List.map3 (\label r ab -> ( ab, r, label )) labels rs
            in
            geoFeatureCollection (List.map geom quad)


stack : List ProjectionProperty -> Spec -> Spec
stack proj geoData =
    asSpec
        [ configure (configuration (coView [ vicoStroke Nothing ]) [])
        , width 200
        , height 200
        , projection proj
        , dataFromJson geoData [ jsonProperty "features" ]
        , geoshape [ maStroke "#fff" ]
        , encoding (color [ mName "properties.cat", mMType Nominal, mLegend [ leTitle "" ] ] [])
        ]
```
