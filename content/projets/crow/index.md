+++
date = '2025-06-12T09:05:05+02:00'
title = 'Crow'
summary = "Crow est une bibliothèque Go conçue pour créer des applications en ligne de commande de manière simple et intuitive en utilisant les struct fields et les tags."
tags = [ "Go", "Projects"]
+++
Crow est une bibliothèque Go dont le principale objectif est de fournir une manière simple et intuitive de créer des applications en ligne de commande en utilisant les `struct field` et les `tags`. Inspirée par des projets comme [Commandeer][commandeer], Crow vise à fournir une solution plus directe et "plug & play" pour la création de petites applications ou scripts, réduissant ainsi la complexité souvent associé à des bibliothèques comme [Cobra][cobra].

{{< alert >}}
Crow est dans sa phase initial de développement. Le projet est à considérer comme expérimental pour le moment.
{{< /alert >}}

<br>

{{< github repo="ARTSYS-H/crow" showThumbnail=false >}}

## Origine du Projet

Crow a été développé pour répondre à un besoin personnel et  spécifique : la création rapide et simple de petites applications en ligne de commande. Personnellement, je me suis souvent retrouvé à créer des scripts et petites applications où des bibliothèques comme [Cobra][cobra] introduisaient une complexité inutile. De plus, des outils comme [Commandeer][commandeer], qui partagent une approche similaire, semblent avoir perdu de leur dynamisme en termes de maintenance. Crow est donc né pour offrir une alternative simple et bien maintenue.

## Caractéristiques et Fonctionnalités


- **Simplicité**: Créez des applications CLI avec un minimum de code et de configuration.
- **Utilisation de Structs et Tags**: Définissez vos commandes et options directement dans des structures Go avec des tags.
- **Génération Automatique de Messages d'Aide**: Les messages d'aide pour vos commandes sont générés automatiquement, facilitant la documentation et l'utilisation de votre application.
- **Plug and Play**: Conçu pour être facile à intégrer et à utiliser sans configuration complexe.
- **Idéal pour les Petits Projets**: Parfait pour les scripts et petites applications où des bibliothèques comme Cobra seraient excessives.

> :sparkles: **Nouveau**: Il est maintenant possible de créer des topics d'aides additionnels.

## Utilisation

Voici un petit exemple simple :

```go
package main

import (
    "github.com/ARTSYS-H/crow/pkg/crow"
)

type MyCommand struct {
    Name string `help:"You Name"`
    Age  int    `help:"Your age"`
}

func (mc *MyCommand) Run() error {
    // Do your stuff here
}

func main() {
    app := crow.New("App Name", "App Description")
    command := &MyCommand{
        Name: "Lucas",
        Age: 27,
    }
    app.AddCommand(command, "Description of the command")
    err = app.Execute(os.Args)
    if err != nil {
        fmt.Println(err)
        os.Exit(1)
    }
}
```

## Conclusion

Pour le moment, la librairie Crow ne couvre que le minimum essentiel pour couvrir mes besoins lors de création de petit CLI. Elle offre tout de même une manière simple et direct de créer des commandes et d'accompagner l'application d'une documentation. La prochaine phase, serait d'améliorer la qualité du code, les performances et peut-être même de trouver de meilleurs solutions d'implémentation.

[commandeer]: https://github.com/jaffee/commandeer
[cobra]: https://github.com/spf13/cobra
