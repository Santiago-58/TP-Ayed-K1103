#include <iostream>
#include <conio.h>
#include <fstream>
#include <string>
#include <clocale>
#include <cstdio>

using namespace std;

// Estructura utilizada en memoria
struct Orden {
    int x = 0;
    int y = 0;
    unsigned int espera = 0;
    bool soltarGranada1 = false;
    bool soltarGranada2 = false;
    bool ataqueKamikaze = false;
    bool aterrizaje = false;
    bool despegue = false;
    int siguientex = 0;
    int siguientey = 0;
};

// Estructura utilizada para guardar registros en el archivo
struct OrdenArchivo {
    int x = 0;
    int y = 0;
    unsigned int espera = 0;
    bool soltarGranada1 = false;
    bool soltarGranada2 = false;
    bool ataqueKamikaze = false;
    bool aterrizaje = false;
    bool despegue = false;
    int siguientex = 0;
    int siguientey = 0;
};

// Matriz de memoria
Orden memoria[200][200];
bool ocupado[200][200];

// -----------------------------------------
// OPERACION 1: CARGAR ARCHIVO DE ATAQUE
// -----------------------------------------

void cargararchivoatq() {

    char ruta[256];

    cout << "Archivo de ataque: ";
    cin >> ruta;

    FILE *archivo = fopen(ruta, "rb");

    if (!archivo) {
        cout << "No abre el archivo" << endl;
        return;
    }

    Orden ordenVacia;

    for (int i = 0; i < 200; i++) {
        for (int j = 0; j < 200; j++) {
            memoria[i][j] = ordenVacia;
            ocupado[i][j] = false;
        }
    }

    OrdenArchivo reg;

    int despegues = 0;
    int finales = 0;
    int leidos = 0;
    int actX = -1;
    int actY = -1;

    bool err = false;

    while (fread(&reg, sizeof(OrdenArchivo), 1, archivo)) {

        leidos++;

        // Validar coordenadas
        if (reg.x < 0 || reg.x >= 200 ||
            reg.y < 0 || reg.y >= 200) {
            err = true;
            break;
        }

        // No permitir dos ordenes en la misma posicion
        if (ocupado[reg.x][reg.y]) {
            err = true;
            break;
        }

        if (reg.despegue) {

            despegues++;
            actX = reg.x;
            actY = reg.y;

            if (reg.soltarGranada1 ||
                reg.soltarGranada2 ||
                reg.ataqueKamikaze) {
                err = true;
            }
        }

        if (reg.aterrizaje || reg.ataqueKamikaze) {
            finales++;
        }

        memoria[reg.x][reg.y].x = reg.x;
        memoria[reg.x][reg.y].y = reg.y;
        memoria[reg.x][reg.y].espera = reg.espera;
        memoria[reg.x][reg.y].soltarGranada1 = reg.soltarGranada1;
        memoria[reg.x][reg.y].soltarGranada2 = reg.soltarGranada2;
        memoria[reg.x][reg.y].ataqueKamikaze = reg.ataqueKamikaze;
        memoria[reg.x][reg.y].aterrizaje = reg.aterrizaje;
        memoria[reg.x][reg.y].despegue = reg.despegue;
        memoria[reg.x][reg.y].siguientex = reg.siguientex;
        memoria[reg.x][reg.y].siguientey = reg.siguientey;

        ocupado[reg.x][reg.y] = true;
    }

    fclose(archivo);

    if (despegues != 1 || finales != 1) {
        err = true;
    }

    bool visitado[200][200] = {false};

    while (!err && actX != -1) {

        if (visitado[actX][actY]) {
            err = true;
            break;
        }

        visitado[actX][actY] = true;

        Orden act = memoria[actX][actY];

        if (act.aterrizaje || act.ataqueKamikaze) {
            break;
        }

        int difx = act.siguientex - actX;
        int dify = act.siguientey - actY;

        if (difx * difx > 1 || dify * dify > 1) {
            err = true;
            break;
        }

        // Verificar que la siguiente posicion exista
        if (act.siguientex < 0 || act.siguientex >= 200 ||
            act.siguientey < 0 || act.siguientey >= 200 ||
            !ocupado[act.siguientex][act.siguientey]) {
            err = true;
            break;
        }

        actX = act.siguientex;
        actY = act.siguientey;
    }

    if (err) {

        for (int i = 0; i < 200; i++) {
            for (int j = 0; j < 200; j++) {
                memoria[i][j] = ordenVacia;
                ocupado[i][j] = false;
            }
        }

        cout << "ERROR: no se pudo validar" << endl;
    }
    else {
        cout << "Cargado exitosamente" << endl;
    }

    return;
}

// -----------------------------------------
// OPERACION 2: MOSTRAR ATAQUE CARGADO
// -----------------------------------------

void mostraratqcargado() {

    int inicioX = -1;
    int inicioY = -1;

    for (int i = 0; i < 200; i++) {

        for (int j = 0; j < 200; j++) {

            if (memoria[i][j].despegue && ocupado[i][j]) {
                inicioX = i;
                inicioY = j;
                break;
            }
        }

        if (inicioX != -1) {
            break;
        }
    }

    if (inicioX == -1) {
        cout << "Error, no hay ataques cargados en la memoria" << endl;
        return;
    }

    int actX = inicioX;
    int actY = inicioY;
    int lineasimpresas = 0;

    cout << "___ Inicio del ataque ___ " << endl;

    while (true) {

        Orden &act = memoria[actX][actY];

        cout << "Posicion [" << actX << "][" << actY << "]: ";

        if (act.despegue) cout << "Despegue. ";
        if (act.espera > 0) cout << "Espera " << act.espera << "seg. ";
        if (act.soltarGranada1) cout << "Granada 1. ";
        if (act.soltarGranada2) cout << "Granada 2. ";
        if (act.aterrizaje) cout << "Aterrizaje. ";
        if (act.ataqueKamikaze) cout << "Kamikaze. ";

        cout << "(Siguiente: " << act.siguientex
             << "," << act.siguientey << ")" << endl;

        lineasimpresas++;

        if (lineasimpresas >= 20) {
            cout << "\n-- Presione cualquier tecla para ver la siguiente pagina --";
            getch();
            lineasimpresas = 0;
            cout << "\n";
        }

        if (act.aterrizaje || act.ataqueKamikaze) {
            break;
        }

        actX = act.siguientex;
        actY = act.siguientey;
    }

    cout << "___ Fin del Ataque ___" << endl;

    return;
}

// -----------------------------------------
// FUNCION PARA INGRESAR LOS DATOS DE UNA ORDEN
// -----------------------------------------

void ingresarDatos(OrdenArchivo &orden) {

    int dato;

    cout << "\nCoordenada X: ";
    cin >> orden.x;

    cout << "Coordenada Y: ";
    cin >> orden.y;

    cout << "Tiempo de espera: ";
    cin >> orden.espera;

    cout << "Soltar granada 1 (1 = Si / 0 = No): ";
    cin >> dato;
    orden.soltarGranada1 = (dato != 0);

    cout << "Soltar granada 2 (1 = Si / 0 = No): ";
    cin >> dato;
    orden.soltarGranada2 = (dato != 0);

    cout << "Ataque kamikaze (1 = Si / 0 = No): ";
    cin >> dato;
    orden.ataqueKamikaze = (dato != 0);

    cout << "Aterrizaje (1 = Si / 0 = No): ";
    cin >> dato;
    orden.aterrizaje = (dato != 0);

    cout << "Despegue (1 = Si / 0 = No): ";
    cin >> dato;
    orden.despegue = (dato != 0);

    cout << "Siguiente X: ";
    cin >> orden.siguientex;

    cout << "Siguiente Y: ";
    cin >> orden.siguientey;
}

// -----------------------------------------
// FUNCION PARA MOSTRAR UN REGISTRO
// -----------------------------------------

void mostrarRegistro(OrdenArchivo orden) {

    cout << "\n----- DATOS DE LA ORDEN -----\n";

    cout << "Coordenada X: " << orden.x << endl;
    cout << "Coordenada Y: " << orden.y << endl;
    cout << "Tiempo de espera: " << orden.espera << endl;

    cout << "Granada 1: " << orden.soltarGranada1 << endl;
    cout << "Granada 2: " << orden.soltarGranada2 << endl;

    cout << "Ataque kamikaze: " << orden.ataqueKamikaze << endl;
    cout << "Aterrizaje: " << orden.aterrizaje << endl;
    cout << "Despegue: " << orden.despegue << endl;

    cout << "Siguiente X: " << orden.siguientex << endl;
    cout << "Siguiente Y: " << orden.siguientey << endl;

    cout << "-----------------------------\n";
}

// -----------------------------------------
// OPERACION 3: CREAR ARCHIVO DE ATAQUE
// -----------------------------------------

void crearArchivoAtaque() {

    string ruta;
    char continuar;

    OrdenArchivo orden;

    cout << "\nIngrese el nombre del archivo: ";
    cin >> ruta;

    // Agregar extension .dat si no fue ingresada
    if (ruta.size() < 4 ||
        ruta.substr(ruta.size() - 4) != ".dat") {
        ruta += ".dat";
    }

    // Comprobar si el archivo ya existe
    ifstream existente(ruta.c_str(), ios::binary);

    if (existente.good()) {
        cout << "\nEl archivo ya existe.\n";
        return;
    }

    existente.close();

    // Crear el archivo binario
    ofstream archivo(ruta.c_str(), ios::binary);

    if (!archivo) {
        cout << "\nNo se pudo crear el archivo.\n";
        return;
    }

    do {

        cout << "\nIngrese los datos de la orden:\n";

        ingresarDatos(orden);

        // Guardar la estructura completa en el archivo
        archivo.write(reinterpret_cast<char*>(&orden),
                      sizeof(OrdenArchivo));

        if (!archivo) {
            cout << "\nError al guardar la orden.\n";
            break;
        }

        cout << "\nDesea ingresar otra orden? (s/n): ";
        cin >> continuar;

    } while (continuar == 's' || continuar == 'S');

    archivo.close();

    cout << "\nArchivo creado correctamente.\n";
}

// -----------------------------------------
// OPERACION 4: CORREGIR REGISTRO
// -----------------------------------------

void corregirRegistroArchivo() {

    string ruta;
    int x, y;

    OrdenArchivo orden;
    bool encontrado = false;

    cout << "\nIngrese el nombre del archivo: ";
    cin >> ruta;

    // Agregar extension .dat si es necesario
    if (ruta.size() < 4 ||
        ruta.substr(ruta.size() - 4) != ".dat") {
        ruta += ".dat";
    }

    // Abrir archivo para lectura y escritura binaria
    fstream archivo(ruta.c_str(),
                    ios::in | ios::out | ios::binary);

    if (!archivo) {
        cout << "\nNo se pudo abrir el archivo.\n";
        return;
    }

    cout << "\nIngrese la coordenada X del registro: ";
    cin >> x;

    cout << "Ingrese la coordenada Y del registro: ";
    cin >> y;

    // Recorrer los registros del archivo
    while (true) {

        // Guardar la posicion del registro actual
        streampos posicion = archivo.tellg();

        // Leer un registro
        if (!archivo.read(reinterpret_cast<char*>(&orden),
                          sizeof(OrdenArchivo))) {
            break;
        }

        // Comparar las coordenadas
        if (orden.x == x && orden.y == y) {

            encontrado = true;

            cout << "\nRegistro encontrado.\n";

            mostrarRegistro(orden);

            cout << "\nIngrese los nuevos datos:\n";

            ingresarDatos(orden);

            // Volver a la posicion del registro encontrado
            archivo.seekp(posicion);

            // Reemplazar el registro anterior
            archivo.write(reinterpret_cast<char*>(&orden),
                          sizeof(OrdenArchivo));

            if (archivo) {
                cout << "\nRegistro actualizado correctamente.\n";
            }
            else {
                cout << "\nError al actualizar el registro.\n";
            }

            break;
        }
    }

    if (!encontrado) {
        cout << "\nNo se encontro ningun registro con esas coordenadas.\n";
    }

    archivo.close();
}

// -----------------------------------------
// OPERACION 5: CORREGIR UN REGISTRO EN MEMORIA
// -----------------------------------------

void corregirRegistroMemoria() {

    int x, y;
    OrdenArchivo reg;

    cout << "\nIngrese la coordenada X del registro: ";
    cin >> x;

    cout << "\nIngrese la coordenada Y del registro: ";
    cin >> y;

    // Validar coordenadas
    if (x < 0 || x >= 200 || y < 0 || y >= 200) {
        cout << "\nLas coordenadas deben estar entre 0 y 199.\n";
        return;
    }

    if (!ocupado[x][y]) {
        cout << "\nNo hay ninguna orden en esa coordenada.\n";
        return;
    }

    // Copiar la orden de memoria a OrdenArchivo
    reg.x = x;
    reg.y = y;
    reg.espera = memoria[x][y].espera;
    reg.soltarGranada1 = memoria[x][y].soltarGranada1;
    reg.soltarGranada2 = memoria[x][y].soltarGranada2;
    reg.ataqueKamikaze = memoria[x][y].ataqueKamikaze;
    reg.aterrizaje = memoria[x][y].aterrizaje;
    reg.despegue = memoria[x][y].despegue;
    reg.siguientex = memoria[x][y].siguientex;
    reg.siguientey = memoria[x][y].siguientey;

    mostrarRegistro(reg);

    cout << "\nIngrese los nuevos datos:\n";

    ingresarDatos(reg);

    // Volver a copiar los datos a memoria
    memoria[x][y].espera = reg.espera;
    memoria[x][y].soltarGranada1 = reg.soltarGranada1;
    memoria[x][y].soltarGranada2 = reg.soltarGranada2;
    memoria[x][y].ataqueKamikaze = reg.ataqueKamikaze;
    memoria[x][y].aterrizaje = reg.aterrizaje;
    memoria[x][y].despegue = reg.despegue;
    memoria[x][y].siguientex = reg.siguientex;
    memoria[x][y].siguientey = reg.siguientey;

    cout << "\nRegistro corregido en memoria.\n";
}

// -----------------------------------------
// OPERACION 6: GUARDAR MEMORIA EN UN ARCHIVO NUEVO
// -----------------------------------------

void guardamemoria() {

    char ruta[256];

    cout << "Ingrese ruta y nombre del archivo nuevo a guardar: ";
    cin >> ruta;

    FILE *archivo = fopen(ruta, "wb");

    if (!archivo) {
        cout << "NO SE PUEDE CREAR EL ARCHIVO" << endl;
        return;
    }

    OrdenArchivo reg;

    for (int i = 0; i < 200; i++) {

        for (int j = 0; j < 200; j++) {

            if (ocupado[i][j]) {

                reg.x = i;
                reg.y = j;

                reg.espera = memoria[i][j].espera;
                reg.soltarGranada1 = memoria[i][j].soltarGranada1;
                reg.soltarGranada2 = memoria[i][j].soltarGranada2;
                reg.ataqueKamikaze = memoria[i][j].ataqueKamikaze;
                reg.aterrizaje = memoria[i][j].aterrizaje;
                reg.despegue = memoria[i][j].despegue;
                reg.siguientex = memoria[i][j].siguientex;
                reg.siguientey = memoria[i][j].siguientey;

                fwrite(&reg, sizeof(OrdenArchivo), 1, archivo);
            }
        }
    }

    fclose(archivo);

    cout << "Memoria guardada en: " << ruta << endl;
}

// -----------------------------------------
// OPERACION 7: VISUALIZAR ARCHIVO DE ATAQUE EN HTML
// -----------------------------------------

void visualizarHTML() {

    char ruta[256];

    cout << "\nIngrese el nombre del archivo HTML: ";
    cin >> ruta;

    // Agregar extension .html si no fue ingresada
    string nombre = ruta;

    if (nombre.size() < 5 ||
        nombre.substr(nombre.size() - 5) != ".html") {
        nombre += ".html";
    }

    // Comprobar que haya un ataque cargado en memoria
    bool hayAtaque = false;

    for (int i = 0; i < 200; i++) {
        for (int j = 0; j < 200; j++) {

            if (ocupado[i][j]) {
                hayAtaque = true;
                break;
            }
        }

        if (hayAtaque) {
            break;
        }
    }

    if (!hayAtaque) {
        cout << "\nNo hay ningun ataque cargado en memoria.\n";
        return;
    }

    // Crear archivo HTML
    FILE *archivo = fopen(nombre.c_str(), "w");

    if (archivo == NULL) {
        cout << "\nNo se pudo crear el archivo HTML.\n";
        return;
    }

    // Inicio del documento HTML
    fprintf(archivo, "<!DOCTYPE html>\n");
    fprintf(archivo, "<html>\n");
    fprintf(archivo, "<head>\n");
    fprintf(archivo, "<meta charset=\"UTF-8\">\n");
    fprintf(archivo, "<title>Visualizacion del ataque</title>\n");

    // Estilos basicos de la tabla
    fprintf(archivo, "<style>\n");
    fprintf(archivo, "table { border-collapse: collapse; }\n");
    fprintf(archivo, "td { border: 1px solid black; ");
    fprintf(archivo, "width: 65px; height: 45px; ");
    fprintf(archivo, "text-align: center; font-size: 10px; }\n");
    fprintf(archivo, "</style>\n");

    fprintf(archivo, "</head>\n");
    fprintf(archivo, "<body>\n");

    fprintf(archivo, "<h2>Tabla de ataques del dron</h2>\n");
    fprintf(archivo, "<table>\n");

    // Recorrer las 200 filas y columnas
    for (int i = 0; i < 200; i++) {

        fprintf(archivo, "<tr>\n");

        for (int j = 0; j < 200; j++) {

            fprintf(archivo, "<td>");

            // Si la posicion esta vacia, mostrar un guion
            if (!ocupado[i][j]) {

                fprintf(archivo, "-");

            }
            else {

                Orden &act = memoria[i][j];

                if (act.despegue)
                    fprintf(archivo, "Despegue<br>");

                if (act.aterrizaje)
                    fprintf(archivo, "Aterrizaje<br>");

                if (act.soltarGranada1)
                    fprintf(archivo, "Granada 1<br>");

                if (act.soltarGranada2)
                    fprintf(archivo, "Granada 2<br>");

                if (act.ataqueKamikaze)
                    fprintf(archivo, "Kamikaze<br>");

                if (act.espera > 0)
                    fprintf(archivo, "Espera: %u seg<br>", act.espera);

                fprintf(archivo, "Sig: (%d,%d)",
                        act.siguientex, act.siguientey);
            }

            fprintf(archivo, "</td>\n");
        }

        fprintf(archivo, "</tr>\n");
    }

    // Cerrar tabla y documento
    fprintf(archivo, "</table>\n");
    fprintf(archivo, "</body>\n");
    fprintf(archivo, "</html>\n");

    fclose(archivo);

    cout << "\nArchivo HTML generado correctamente: "
         << nombre << endl;
}

// -----------------------------------------
// PROGRAMA PRINCIPAL
// -----------------------------------------

int main() {

    setlocale(LC_ALL, "");

    int opcion;

    do {

        cout << "\n====================================\n";
        cout << "       SISTEMA DE ATAQUE DE DRONES\n";
        cout << "====================================\n";

        cout << "1. Cargar memoria de ataque\n";
        cout << "2. Visualizar memoria de ataque\n";
        cout << "3. Crear archivo de ataque\n";
        cout << "4. Corregir registro de archivo\n";
        cout << "5. Corregir un registro en memoria\n";
        cout << "6. Guardar memoria en un archivo nuevo\n";
        cout << "7. Visualizar un archivo de ataque en HTML\n";
        cout << "0. Salir\n";

        cout << "\nSeleccione una opcion: ";
        cin >> opcion;

        switch (opcion) {

            case 1:
                cargararchivoatq();
                break;

            case 2:
                mostraratqcargado();
                break;

            case 3:
                crearArchivoAtaque();
                break;

            case 4:
                corregirRegistroArchivo();
                break;

            case 5:
                corregirRegistroMemoria();
                break;

            case 6:
                guardamemoria();
                break;

            case 7:
                visualizarHTML();
                break;

            case 0:
                cout << "\nFinalizando sistema de drones...\n";
                break;

            default:
                cout << "\nOpcion incorrecta. Intente nuevamente.\n";
                break;
        }

    } while (opcion != 0);

    return 0;
}
