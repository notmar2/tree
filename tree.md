<!-- LTeX: language=es -->
## Documentación Árbol
El documento inicia con los elementos típicos

```C++
#ifndef TREE_H
#define TREE_H

#include <iostream>
```

Nuestras macros de definición y la macro de inclusión de iostream para tener acceso a los elementos necesarios para imprimir a la consola.

Posteriormente tenemos la definición de 2 clases. La primera es la clase Node.

```C++
template <typename T>
class Node {
public:
    T data;
    Node* firstChild;
    Node* nextSibling;

    explicit Node(T data);
};
```

Esta clase utiliza template para poder tomar cualquier tipo de dato. Tiene un objeto `data` de tipo T (el nombre temporal del tipo de dato, pero cuando se instancie un objeto de esta clase tomará el tipo de dato que le demos a la instancia); y dos punteros, `firstChild` y `nextSibling` los cuales se utilizan para apuntar al primer hijo del nodo actual y al siguiente nodo hermano (otro nodo que sea hijo del nodo padre del nodo actual). 

Tiene un constructor que toma un parámetro de tipo T para llenar `data`, se declara como explícito para evitar que el compilador realice castings implícitos y se define de la siguiente forma:

```C++
template <typename T>
Node<T>::Node(T data) : data(data), firstChild(nullptr), nextSibling(nullptr) {}
```

Como se puede observar lo único que este constructor hace es asignar el parámetro `data` a nuestro elemento de `data` en el objeto y asignar nuestros punteros al hijo y hermano a punteros nulos, 

La siguiente clase es la clase Tree.

```C++
template <typename T>
class Tree {
public:
    Node<T>* root;

    Tree();

    Node<T>* addNode(T data, Node<T>* parent = nullptr);

    void printTree(Node<T>* node, int level = 0);
};
```

También utiliza templates para tomar distintos tipos de dato. Contiene un puntero a un objeto Node de tipo T el cual será la raíz de nuestro árbol (la primera entrada), un constructor default y dos métodos.

El constructor se define de la siguiente manera:

```C++
template <typename T>
Tree<T>::Tree() : root(nullptr) {}
```

Lo único que hace es asignar el puntero de `root` a un puntero nulo.

El primer método, `addNode` se utilizará para añadir nuevos nodos al árbol y se define de la siguiente manera:

```C++
template <typename T>
Node<T>* Tree<T>::addNode(T data, Node<T>* parent) {
    Node<T>* newNode = new Node<T>(data);

    if(parent) {
        if(parent->firstChild) {
            Node<T>* sibling = parent->firstChild;
            while(sibling->nextSibling) {
                sibling = sibling->nextSibling;
            }
            sibling->nextSibling = newNode;
        } else {
            parent->firstChild = newNode;
        }
    } else {
        root = newNode;
    }

    return newNode;
}
```
El método toma como parámetros dos elementos, un objeto de tipo T que se utilizará para llenar el apartado de data de nuestro nuevo nodo y un puntero al nodo padre. Esencialmente lo que se hace es crear un nuevo nodo de tipo T con el dato y asignarlo a un puntero. Enseguida viene un sistema de ifs que se utiliza para determinar en dónde deberá ir nuestro nuevo nodo. Si no se tiene un puntero a un nodo padre (es decir el nuevo nodo es el primero de nuestra estructura, se asigna root a nuestro nodo creado. En caso de que si se tenga, se revisa si existe un primer hijo en el padre, en caso de que sea asi se asgina dicho hijo como el hermano del nodo actual y luego se cicla por todos los nodos hermanos hasta que se llegue al final (cuando ya no haya nodo hermano) moviendo el hermano hasta ese punto (de forma que el nodo actual sea el último en la cadena de hermanos). Si no existe un primer hijo simplemente se asigna el nodo actual como el primer hijo del hermano.

El segundo y último método simplemente imprime el árbol:

```C++
template <typename T>
void Tree<T>::printTree(Node<T>* node, int level) {
    if(!node) return;

    for(int i = 0; i < level; i++) std::cout << "--";
    std::cout << node->data << '\n';

    printTree(node->firstChild, level + 1);
    printTree(node->nextSibling, level);
}
```

Retorna si el nodo es un puntero nulo (pues no se puede imprimir), en caso de no retornar, utiliza un ciclo for para imprimir líneas por cada nivel del nodo actual, es decir que los nodos más adelante en la recursión tendrán más líneas, después se imprime el dato del nodo que se paso a la función y se llama la función recursivamente al primer hijo del nodo actual y al siguiente hermano, garantizando que se impriman todos.

Al final del header tenemos las siguientes líneas:

```C++
#include "Tree.cpp"

#endif //TREE_H
```

Simplemente se usan para terminar el bloque de código, de la misma forma que en los otros trabajos hay que mencionar que incluir el cpp de esta forma en nuestro header es una mala práctica y es posible que el código no compile apropiadamente. En lugar de eso se incluye el header en el cpp solamente con la siguiente línea:

```C++
#include "Tree.h"
```

La cual si esta presente en el código, pero solamente debería incluirse en esta parte.
