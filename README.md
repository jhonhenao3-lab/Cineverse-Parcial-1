# Cineverse-Parcial-1
Esta aplicación se ajusta a los requerimientos del proyecto para el parcial 1.

package com.example.myapplication_sala_de_cine_parcial_1_

import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.annotation.DrawableRes
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.items
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Favorite
import androidx.compose.material.icons.filled.FavoriteBorder
import androidx.compose.material.icons.filled.Star
import androidx.compose.material3.*
import androidx.compose.runtime.*
import androidx.compose.ui.Alignment
import androidx.compose.ui.Modifier
import androidx.compose.ui.graphics.Color
import androidx.compose.ui.text.TextStyle
import androidx.compose.ui.text.font.FontFamily
import androidx.compose.ui.text.font.FontWeight
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import androidx.navigation.NavType
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.compose.rememberNavController
import androidx.navigation.navArgument

// ============================================================================
// 1. MODELO DE DATOS Y REPOSITORIO
// ============================================================================

data class Movie(
    val id: Int,
    val title: String,
    val genre: String,
    val rating: Double,
    val duration: String,
    val synopsis: String,
    val price: Double,
    val showtimes: List<String>,
    @DrawableRes val imageRes: Int = 0,
    val isFavorite: Boolean = false
)

object MovieRepository {
    val movies = listOf(
        Movie(
            id = 1,
            title = "Inception: El Origen",
            genre = "Ciencia Ficción",
            rating = 4.8,
            duration = "2h 28m",
            synopsis = "Un ladrón que roba secretos corporativos a través del uso de la tecnología de intercambio de sueños.",
            price = 15.0,
            showtimes = listOf("14:30", "17:45", "21:00")
        ),
        Movie(
            id = 2,
            title = "Interstellar",
            genre = "Ciencia Ficción",
            rating = 4.9,
            duration = "2h 49m",
            synopsis = "Un equipo de exploradores viaja a través de un agujero de gusano en el espacio en un intento por garantizar la supervivencia de la humanidad.",
            price = 16.5,
            showtimes = listOf("15:00", "18:30", "22:15")
        ),
        Movie(
            id = 3,
            title = "The Dark Knight",
            genre = "Acción",
            rating = 4.9,
            duration = "2h 32m",
            synopsis = "Batman se enfrenta al Guasón, un criminal caótico que busca sumir a Ciudad Gótica en la anarquía.",
            price = 14.0,
            showtimes = listOf("16:00", "19:15", "22:00")
        ),
        Movie(
            id = 4,
            title = "Spider-Man: Across the Spider-Verse",
            genre = "Animación",
            rating = 4.7,
            duration = "2h 20m",
            synopsis = "Miles Morales es catapultado a través del Multiverso, donde se encuentra con un equipo de Spider-Gentes.",
            price = 13.5,
            showtimes = listOf("13:00", "15:45", "18:30")
        ),
        Movie(
            id = 5,
            title = "Oppenheimer",
            genre = "Drama",
            rating = 4.6,
            duration = "3h 00m",
            synopsis = "La historia del físico estadounidense J. Robert Oppenheimer y su papel en el desarrollo de la bomba atómica.",
            price = 17.0,
            showtimes = listOf("14:00", "17:30", "21:00")
        ),
        Movie(
            id = 6,
            title = "Avatar: El Camino del Agua",
            genre = "Aventura",
            rating = 4.5,
            duration = "3h 12m",
            synopsis = "Jake Sully vive con su nueva familia en el planeta Pandora hasta que una amenaza regresa a terminar lo que se empezó.",
            price = 18.0,
            showtimes = listOf("15:30", "19:00", "22:30")
        )
    )
}

// ============================================================================
// 2. CONFIGURACIÓN DEL TEMA MATERIAL 3
// ============================================================================

private val icons: Any = Any()
val DarkBackground = Color(0xFF121214)
val SurfaceDark = Color(0xFF1E1E24)
val PrimaryGold = Color(0xFFFFC107)
val SecondaryBlue = Color(0xFF2196F3)
val TextLight = Color(0xFFEEEEEE)
val TextGray = Color(0xFFAAAAAA)

val CineTypography = Typography(
    titleLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Bold,
        fontSize = 22.sp,
        color = TextLight
    ),
    bodyMedium = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 14.sp,
        color = TextGray
    )
)

private val DarkColorScheme = darkColorScheme(
    primary = PrimaryGold,
    secondary = SecondaryBlue,
    background = DarkBackground,
    surface = SurfaceDark,
    onPrimary = DarkBackground,
    onBackground = TextLight,
    onSurface = TextLight
)

@Composable
fun CineVerseTheme(content: @Composable () -> Unit) {
    MaterialTheme(
        colorScheme = DarkColorScheme,
        typography = CineTypography,
        content = content
    )
}

// ============================================================================
// 3. COMPONENTES REUTILIZABLES (@Composable)
// ============================================================================

// Componente Reutilizable 1: Tarjeta para representar cada película
@Composable
fun MovieCard(
    movie: Movie,
    onMovieClick: (Movie) -> Unit,
    onFavoriteToggle: (Movie) -> Unit,
    modifier: Modifier = Modifier
) {
    Card(
        modifier = modifier
            .fillMaxWidth()
            .padding(vertical = 6.dp)
            .clickable { onMovieClick(movie) },
        colors = CardDefaults.cardColors(containerColor = MaterialTheme.colorScheme.surface)
    ) {
        Row(
            modifier = Modifier
                .padding(16.dp)
                .fillMaxWidth(),
            horizontalArrangement = Arrangement.SpaceBetween,
            verticalAlignment = Alignment.CenterVertically
        ) {
            Column(modifier = Modifier.weight(1f)) {
                Text(
                    text = movie.title,
                    style = MaterialTheme.typography.titleMedium,
                    fontWeight = FontWeight.Bold,
                    color = MaterialTheme.colorScheme.onSurface
                )
                Spacer(modifier = Modifier.height(4.dp))
                Text(
                    text = "${movie.genre} • ${movie.duration}",
                    style = MaterialTheme.typography.bodySmall,
                    color = MaterialTheme.colorScheme.onSurface.copy(alpha = 0.7f)
                )
                Spacer(modifier = Modifier.height(6.dp))
                Row(verticalAlignment = Alignment.CenterVertically) {
                    Icon(
                        imageVector = Icons.Default.Star,
                        contentDescription = "Calificación",
                        tint = MaterialTheme.colorScheme.primary,
                        modifier = Modifier.size(16.dp)
                    )
                    Spacer(modifier = Modifier.width(4.dp))
                    Text(
                        text = movie.rating.toString(),
                        style = MaterialTheme.typography.bodySmall,
                        fontWeight = FontWeight.Bold
                    )
                }
            }

            IconButton(onClick = { onFavoriteToggle(movie) }) {
                Icon(
                    imageVector = if (movie.isFavorite) Icons.Default.Favorite else Icons.Default.FavoriteBorder,
                    contentDescription = "Favorito",
                    tint = if (movie.isFavorite) MaterialTheme.colorScheme.primary else MaterialTheme.colorScheme.onSurface
                )
            }
        }
    }
}

// Componente Reutilizable 2: Encabezado superior unificado
@Composable
fun HeaderBanner(title: String, subtitle: String) {
    Column(
        modifier = Modifier
            .fillMaxWidth()
            .background(MaterialTheme.colorScheme.surface)
            .padding(16.dp)
    ) {
        Text(
            text = title,
            style = MaterialTheme.typography.titleLarge,
            color = MaterialTheme.colorScheme.primary,
            fontWeight = FontWeight.Bold
        )
        Text(
            text = subtitle,
            style = MaterialTheme.typography.bodyMedium
        )
    }
}

// ============================================================================
// 4. PANTALLAS DE LA APLICACIÓN
// ============================================================================

// Pantalla 1: Cartelera
@Composable
fun HomeScreen(
    movies: List<Movie>,
    onMovieSelect: (Movie) -> Unit,
    onFavoriteToggle: (Movie) -> Unit,
    onNavigateToFavorites: () -> Unit
) {
    // Funcionalidad 1: Búsqueda y filtrado dinámico
    var searchQuery by remember { mutableStateOf("") }

    val filteredMovies = remember(searchQuery, movies) {
        movies.filter {
            it.title.contains(searchQuery, ignoreCase = true) ||
                    it.genre.contains(searchQuery, ignoreCase = true)
        }
    }

    Column(modifier = Modifier.fillMaxSize()) {
        HeaderBanner(title = "CineVerse", subtitle = "Cartelera de películas")

        Column(modifier = Modifier.padding(16.dp)) {
            OutlinedTextField(
                value = searchQuery,
                onValueChange = { searchQuery = it },
                label = { Text("Buscar película o género...") },
                modifier = Modifier.fillMaxWidth(),
                singleLine = true
            )

            Spacer(modifier = Modifier.height(8.dp))

            Button(
                onClick = onNavigateToFavorites,
                modifier = Modifier.fillMaxWidth()
            ) {
                Text("Ver mis películas favoritas")
            }

            Spacer(modifier = Modifier.height(16.dp))

            // Lista dinámica (LazyColumn)
            LazyColumn(modifier = Modifier.fillMaxSize()) {
                items(filteredMovies) { movie ->
                    MovieCard(
                        movie = movie,
                        onMovieClick = { onMovieSelect(movie) },
                        onFavoriteToggle = { onFavoriteToggle(movie) }
                    )
                }
            }
        }
    }
}

// Pantalla 2: Favoritos
@Composable
fun FavoritesScreen(
    favoriteMovies: List<Movie>,
    onMovieSelect: (Movie) -> Unit,
    onFavoriteToggle: (Movie) -> Unit,
    onBack: () -> Unit
) {
    Column(modifier = Modifier.fillMaxSize()) {
        HeaderBanner(title = "Mis Favoritos", subtitle = "Películas guardadas para ver luego")

        Column(modifier = Modifier.padding(16.dp)) {
            Button(onClick = onBack, modifier = Modifier.fillMaxWidth()) {
                Text("Volver a la cartelera")
            }

            Spacer(modifier = Modifier.height(16.dp))

            if (favoriteMovies.isEmpty()) {
                Box(modifier = Modifier.fillMaxSize(), contentAlignment = Alignment.Center) {
                    Text("No tienes películas marcadas como favoritas.")
                }
            } else {
                LazyColumn {
                    items(favoriteMovies) { movie ->
                        MovieCard(
                            movie = movie,
                            onMovieClick = { onMovieSelect(movie) },
                            onFavoriteToggle = { onFavoriteToggle(movie) }
                        )
                    }
                }
            }
        }
    }
}

// Pantalla 3: Reserva de Entradas
@Composable
fun BookingScreen(
    movie: Movie,
    onBack: () -> Unit
) {
    // Funcionalidad 3: Cálculo automático e interacciones
    var selectedShowtime by remember { mutableStateOf(movie.showtimes.firstOrNull() ?: "") }
    var ticketCount by remember { mutableIntStateOf(1) }
    var isVip by remember { mutableStateOf(false) }

    val basePrice = movie.price
    val vipSurcharge = if (isVip) 5.0 else 0.0
    val totalPrice = (basePrice + vipSurcharge) * ticketCount

    Column(modifier = Modifier.fillMaxSize()) {
        HeaderBanner(title = "Reserva de Boletos", subtitle = movie.title)

        Column(
            modifier = Modifier
                .padding(16.dp)
                .fillMaxSize(),
            verticalArrangement = Arrangement.SpaceBetween
        ) {
            Column {
                Text(text = "Sinopsis", style = MaterialTheme.typography.titleMedium)
                Spacer(modifier = Modifier.height(4.dp))
                Text(text = movie.synopsis, style = MaterialTheme.typography.bodyMedium)

                Spacer(modifier = Modifier.height(16.dp))

                Text(text = "Selecciona Horario", style = MaterialTheme.typography.titleMedium)
                Spacer(modifier = Modifier.height(8.dp))

                // Lista horizontal interactiva (LazyRow)
                LazyRow(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                    items(movie.showtimes) { time ->
                        FilterChip(
                            selected = (selectedShowtime == time),
                            onClick = { selectedShowtime = time },
                            label = { Text(time) }
                        )
                    }
                }

                Spacer(modifier = Modifier.height(16.dp))

                Text(text = "Cantidad de Boletos", style = MaterialTheme.typography.titleMedium)
                Spacer(modifier = Modifier.height(8.dp))

                Row(
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(16.dp)
                ) {
                    Button(
                        onClick = { if (ticketCount > 1) ticketCount-- },
                        enabled = ticketCount > 1
                    ) {
                        Text("-")
                    }

                    Text(
                        text = "$ticketCount",
                        style = MaterialTheme.typography.titleLarge
                    )

                    Button(
                        onClick = { if (ticketCount < 10) ticketCount++ },
                        enabled = ticketCount < 10
                    ) {
                        Text("+")
                    }
                }

                Spacer(modifier = Modifier.height(16.dp))

                Row(
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.SpaceBetween,
                    modifier = Modifier.fillMaxWidth()
                ) {
                    Text("Experiencia VIP (+ $5.00/boleto)")
                    Switch(
                        checked = isVip,
                        onCheckedChange = { isVip = it }
                    )
                }
            }

            Card(
                modifier = Modifier.fillMaxWidth(),
                colors = CardDefaults.cardColors(containerColor = MaterialTheme.colorScheme.surface)
            ) {
                Column(modifier = Modifier.padding(16.dp)) {
                    // Se especifica java.util.Locale.US para evitar que la app colapse en el dispositivo
                    val formattedPrice = String.format(java.util.Locale.US, "%.2f", totalPrice)

                    Text(
                        text = "Total a pagar: $$formattedPrice",
                        style = MaterialTheme.typography.titleLarge,
                        color = MaterialTheme.colorScheme.primary
                    )
                    Spacer(modifier = Modifier.height(8.dp))
                    Button(
                        onClick = onBack,
                        modifier = Modifier.fillMaxWidth()
                    ) {
                        Text("Confirmar y Volver")
                    }
                }
            }
        }
    }
}

// ============================================================================
// 5. NAVEGACIÓN Y PUNTO DE ENTRADA (MainActivity)
// ============================================================================

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            CineVerseTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    CineApp()
                }
            }
        }
    }
}

@Composable
fun CineApp() {
    val navController = rememberNavController()

    // Estado global de películas para alternar favoritos
    var movieList by remember { mutableStateOf(MovieRepository.movies) }

    NavHost(
        navController = navController,
        startDestination = "home"
    ) {
        // Rutas de navegación (Navigation Compose)
        composable("home") {
            HomeScreen(
                movies = movieList,
                onMovieSelect = { movie ->
                    navController.navigate("booking/${movie.id}")
                },
                onFavoriteToggle = { movie ->
                    movieList = movieList.map {
                        if (it.id == movie.id) it.copy(isFavorite = !it.isFavorite) else it
                    }
                },
                onNavigateToFavorites = {
                    navController.navigate("favorites")
                }
            )
        }

        composable("favorites") {
            FavoritesScreen(
                favoriteMovies = movieList.filter { it.isFavorite },
                onMovieSelect = { movie ->
                    navController.navigate("booking/${movie.id}")
                },
                onFavoriteToggle = { movie ->
                    movieList = movieList.map {
                        if (it.id == movie.id) it.copy(isFavorite = !it.isFavorite) else it
                    }
                },
                onBack = { navController.popBackStack() }
            )
        }

        composable(
            route = "booking/{movieId}",
            arguments = listOf(navArgument("movieId") { type = NavType.IntType })
        ) { backStackEntry ->
            val movieId = backStackEntry.arguments?.getInt("movieId") ?: 1
            val selectedMovie = movieList.firstOrNull { it.id == movieId } ?: movieList.first()

            BookingScreen(
                movie = selectedMovie,
                onBack = { navController.popBackStack() }
            )
        }
    }
}
