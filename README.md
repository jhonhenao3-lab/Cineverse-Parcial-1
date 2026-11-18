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

PARCIAL 2 - NUEVAS FUNCIONALIDADES

package com.example.myapplication_2_parcial

import android.content.Context
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.background
import androidx.compose.foundation.clickable
import androidx.compose.foundation.layout.*
import androidx.compose.foundation.lazy.LazyColumn
import androidx.compose.foundation.lazy.LazyRow
import androidx.compose.foundation.lazy.items
import androidx.compose.foundation.rememberScrollState
import androidx.compose.foundation.text.KeyboardOptions
import androidx.compose.foundation.verticalScroll
import androidx.compose.material.icons.Icons
import androidx.compose.material.icons.filled.Add
import androidx.compose.material.icons.filled.Delete
import androidx.compose.material.icons.filled.Edit
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
import androidx.compose.ui.text.input.KeyboardType
import androidx.compose.ui.unit.dp
import androidx.compose.ui.unit.sp
import androidx.lifecycle.ViewModel
import androidx.lifecycle.ViewModelProvider
import androidx.lifecycle.compose.collectAsStateWithLifecycle
import androidx.lifecycle.viewModelScope
import androidx.lifecycle.viewmodel.compose.viewModel
import androidx.navigation.NavType
import androidx.navigation.compose.NavHost
import androidx.navigation.compose.composable
import androidx.navigation.compose.rememberNavController
import androidx.navigation.navArgument
import androidx.room.*
import kotlinx.coroutines.flow.Flow
import kotlinx.coroutines.flow.SharingStarted
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.flow.combine
import kotlinx.coroutines.flow.stateIn
import kotlinx.coroutines.launch
import java.util.Locale

// ============================================================================
// 1. CAPA DE DATOS: ENTIDADES (ROOM)
// ============================================================================

@Entity(tableName = "movies")
data class MovieEntity(
    @field:PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    val title: String,
    val genre: String,
    val rating: Double,
    val duration: String,
    val synopsis: String,
    val price: Double,
    val showtimes: String,
    val isFavorite: Boolean = false,
) {
    fun getShowtimesList(): List<String> =
        showtimes.split(",").mapNotNull { it.trim().ifEmpty { null } }
}

@Entity(
    tableName = "bookings",
    foreignKeys = [
        ForeignKey(
            entity = MovieEntity::class,
            parentColumns = ["id"],
            childColumns = ["movieId"],
            onDelete = ForeignKey.CASCADE
        )
    ],
    indices = [Index(value = ["movieId"])]
)
data class BookingEntity(
    @field:PrimaryKey(autoGenerate = true)
    val id: Int = 0,
    val movieId: Int,
    val movieTitle: String,
    val customerName: String,
    val showtime: String,
    val ticketCount: Int,
    val isVip: Boolean,
    val totalPrice: Double,
    val status: String = "Activa"
)

// ============================================================================
// 2. CAPA DE DATOS: DAOs
// ============================================================================

@Dao
interface MovieDao {
    @Query("SELECT * FROM movies ORDER BY id DESC")
    fun getAllMovies(): Flow<List<MovieEntity>>

    @Query("SELECT COUNT(*) FROM movies")
    suspend fun getMovieCount(): Int

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertMovie(movie: MovieEntity)

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertAll(movies: List<MovieEntity>)

    @Update
    suspend fun updateMovie(movie: MovieEntity)

    @Delete
    suspend fun deleteMovie(movie: MovieEntity)
}

@Dao
interface BookingDao {
    @Query("SELECT * FROM bookings ORDER BY id DESC")
    fun getAllBookings(): Flow<List<BookingEntity>>

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insertBooking(booking: BookingEntity)

    @Update
    suspend fun updateBooking(booking: BookingEntity)

    @Delete
    suspend fun deleteBooking(booking: BookingEntity)
}

// ============================================================================
// 3. CAPA DE DATOS: BASE DE DATOS ROOM
// ============================================================================

@Database(
    entities = [MovieEntity::class, BookingEntity::class],
    version = 1,
    exportSchema = false
)
abstract class CineDatabase : RoomDatabase() {
    abstract fun movieDao(): MovieDao
    abstract fun bookingDao(): BookingDao

    companion object {
        @Volatile
        private var INSTANCE: CineDatabase? = null

        fun getDatabase(context: Context): CineDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context.applicationContext,
                    CineDatabase::class.java,
                    "cineverse_database"
                ).build()
                INSTANCE = instance
                instance
            }
        }
    }
}

// ============================================================================
// 4. CAPA DE DATOS: REPOSITORIO
// ============================================================================

class CineRepository(
    private val movieDao: MovieDao,
    private val bookingDao: BookingDao
) {
    val allMovies: Flow<List<MovieEntity>> = movieDao.getAllMovies()
    val allBookings: Flow<List<BookingEntity>> = bookingDao.getAllBookings()

    suspend fun populateInitialMoviesIfEmpty() {
        if (movieDao.getMovieCount() == 0) {
            val initialMovies = listOf(
                MovieEntity(
                    title = "Inception: El Origen",
                    genre = "Ciencia Ficción",
                    rating = 4.8,
                    duration = "2h 28m",
                    synopsis = "Un ladrón que roba secretos corporativos mediante el intercambio de sueños.",
                    price = 15.0,
                    showtimes = "14:30, 17:45, 21:00"
                ),
                MovieEntity(
                    title = "Interstellar",
                    genre = "Ciencia Ficción",
                    rating = 4.9,
                    duration = "2h 49m",
                    synopsis = "Exploradores viajan a través de un agujero de gusano para salvar a la humanidad.",
                    price = 16.5,
                    showtimes = "15:00, 18:30, 22:15"
                ),
                MovieEntity(
                    title = "The Dark Knight",
                    genre = "Acción",
                    rating = 4.9,
                    duration = "2h 32m",
                    synopsis = "Batman se enfrenta al Guasón, quien busca sumir a Ciudad Gótica en el caos.",
                    price = 14.0,
                    showtimes = "16:00, 19:15, 22:00"
                ),
                MovieEntity(
                    title = "Spider-Man: Across the Spider-Verse",
                    genre = "Animación",
                    rating = 4.7,
                    duration = "2h 20m",
                    synopsis = "Miles Morales es catapultado a través del Multiverso.",
                    price = 13.5,
                    showtimes = "13:00, 15:45, 18:30"
                ),
                MovieEntity(
                    title = "Oppenheimer",
                    genre = "Drama",
                    rating = 4.6,
                    duration = "3h 00m",
                    synopsis = "La historia de J. Robert Oppenheimer y el desarrollo de la bomba atómica.",
                    price = 17.0,
                    showtimes = "14:00, 17:30, 21:00"
                ),
                MovieEntity(
                    title = "Avatar: El Camino del Agua",
                    genre = "Aventura",
                    rating = 4.5,
                    duration = "3h 12m",
                    synopsis = "Jake Sully protege a su familia en Pandora ante una nueva amenaza.",
                    price = 18.0,
                    showtimes = "15:30, 19:00, 22:30"
                )
            )
            movieDao.insertAll(initialMovies)
        }
    }

    suspend fun insertMovie(movie: MovieEntity) = movieDao.insertMovie(movie)
    suspend fun updateMovie(movie: MovieEntity) = movieDao.updateMovie(movie)
    suspend fun deleteMovie(movie: MovieEntity) = movieDao.deleteMovie(movie)

    suspend fun insertBooking(booking: BookingEntity) = bookingDao.insertBooking(booking)
    suspend fun updateBooking(booking: BookingEntity) = bookingDao.updateBooking(booking)
    suspend fun deleteBooking(booking: BookingEntity) = bookingDao.deleteBooking(booking)
}

// ============================================================================
// 5. VIEWMODEL Y ESTADO DE LA INTERFAZ (MVVM)
// ============================================================================

data class CinemaStats(
    val totalMovies: Int = 0,
    val activeBookings: Int = 0,
    val canceledBookings: Int = 0,
    val totalTicketsSold: Int = 0,
    val totalRevenue: Double = 0.0
)

class CineViewModel(private val repository: CineRepository) : ViewModel() {

    init {
        viewModelScope.launch {
            repository.populateInitialMoviesIfEmpty()
        }
    }

    val movies: StateFlow<List<MovieEntity>> = repository.allMovies
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), emptyList())

    val bookings: StateFlow<List<BookingEntity>> = repository.allBookings
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), emptyList())

    val stats: StateFlow<CinemaStats> = combine(movies, bookings) { movieList, bookingList ->
        val (active, canceled) = bookingList.partition { it.status == "Activa" }
        CinemaStats(
            totalMovies = movieList.size,
            activeBookings = active.size,
            canceledBookings = canceled.size,
            totalTicketsSold = active.sumOf { it.ticketCount },
            totalRevenue = active.sumOf { it.totalPrice }
        )
    }.stateIn(viewModelScope, SharingStarted.WhileSubscribed(5000), CinemaStats())

    fun addMovie(
        title: String,
        genre: String,
        rating: Double,
        duration: String,
        synopsis: String,
        price: Double,
        showtimes: String
    ) {
        viewModelScope.launch {
            repository.insertMovie(
                MovieEntity(
                    title = title,
                    genre = genre,
                    rating = rating,
                    duration = duration,
                    synopsis = synopsis,
                    price = price,
                    showtimes = showtimes
                )
            )
        }
    }

    fun updateMovie(movie: MovieEntity) {
        viewModelScope.launch {
            repository.updateMovie(movie)
        }
    }

    fun deleteMovie(movie: MovieEntity) {
        viewModelScope.launch {
            repository.deleteMovie(movie)
        }
    }

    fun toggleFavorite(movie: MovieEntity) {
        viewModelScope.launch {
            repository.updateMovie(movie.copy(isFavorite = !movie.isFavorite))
        }
    }

    fun createBooking(
        movie: MovieEntity,
        customerName: String,
        showtime: String,
        ticketCount: Int,
        isVip: Boolean,
        totalPrice: Double
    ) {
        viewModelScope.launch {
            repository.insertBooking(
                BookingEntity(
                    movieId = movie.id,
                    movieTitle = movie.title,
                    customerName = customerName,
                    showtime = showtime,
                    ticketCount = ticketCount,
                    isVip = isVip,
                    totalPrice = totalPrice,
                    status = "Activa"
                )
            )
        }
    }

    fun toggleBookingStatus(booking: BookingEntity) {
        val newStatus = if (booking.status == "Activa") "Cancelada" else "Activa"
        viewModelScope.launch {
            repository.updateBooking(booking.copy(status = newStatus))
        }
    }

    fun deleteBooking(booking: BookingEntity) {
        viewModelScope.launch {
            repository.deleteBooking(booking)
        }
    }
}

class CineViewModelFactory(private val repository: CineRepository) : ViewModelProvider.Factory {
    override fun <T : ViewModel> create(modelClass: Class<T>): T {
        if (modelClass.isAssignableFrom(CineViewModel::class.java)) {
            @Suppress("UNCHECKED_CAST")
            return CineViewModel(repository) as T
        }
        throw IllegalArgumentException("Unknown ViewModel class")
    }
}

// ============================================================================
// 6. TEMA MATERIAL 3 (COLORES, TIPOGRAFÍA Y ESQUEMA)
// ============================================================================

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
// 7. COMPONENTES REUTILIZABLES (@Composable)
// ============================================================================

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

@Composable
fun MovieCard(
    movie: MovieEntity,
    onMovieClick: (MovieEntity) -> Unit,
    onFavoriteToggle: (MovieEntity) -> Unit,
    onEditClick: ((MovieEntity) -> Unit)? = null,
    onDeleteClick: ((MovieEntity) -> Unit)? = null
) {
    val formattedPrice = String.format(Locale.US, "%.2f", movie.price)

    Card(
        modifier = Modifier
            .fillMaxWidth()
            .padding(vertical = 6.dp)
            .clickable { onMovieClick(movie) },
        colors = CardDefaults.cardColors(containerColor = MaterialTheme.colorScheme.surface)
    ) {
        Column(modifier = Modifier.padding(16.dp)) {
            Row(
                modifier = Modifier.fillMaxWidth(),
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
                        text = "${movie.genre} • ${movie.duration} • $$formattedPrice",
                        style = MaterialTheme.typography.bodySmall,
                        color = MaterialTheme.colorScheme.onSurface.copy(alpha = 0.75f)
                    )
                    Spacer(modifier = Modifier.height(4.dp))
                    Row(verticalAlignment = Alignment.CenterVertically) {
                        Icon(
                            imageVector = Icons.Default.Star,
                            contentDescription = "Rating",
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

                Row {
                    IconButton(onClick = { onFavoriteToggle(movie) }) {
                        Icon(
                            imageVector = if (movie.isFavorite) Icons.Default.Favorite else Icons.Default.FavoriteBorder,
                            contentDescription = "Favorito",
                            tint = if (movie.isFavorite) MaterialTheme.colorScheme.primary else MaterialTheme.colorScheme.onSurface
                        )
                    }
                    if (onEditClick != null) {
                        IconButton(onClick = { onEditClick(movie) }) {
                            Icon(
                                imageVector = Icons.Default.Edit,
                                contentDescription = "Editar película",
                                tint = MaterialTheme.colorScheme.secondary
                            )
                        }
                    }
                    if (onDeleteClick != null) {
                        IconButton(onClick = { onDeleteClick(movie) }) {
                            Icon(
                                imageVector = Icons.Default.Delete,
                                contentDescription = "Eliminar película",
                                tint = Color(0xFFEF5350)
                            )
                        }
                    }
                }
            }
        }
    }
}

@Composable
fun BookingCard(
    booking: BookingEntity,
    onToggleStatus: (BookingEntity) -> Unit,
    onDelete: (BookingEntity) -> Unit
) {
    val isActive = booking.status == "Activa"
    val formattedTotal = String.format(Locale.US, "%.2f", booking.totalPrice)

    Card(
        modifier = Modifier
            .fillMaxWidth()
            .padding(vertical = 6.dp),
        colors = CardDefaults.cardColors(containerColor = MaterialTheme.colorScheme.surface)
    ) {
        Column(modifier = Modifier.padding(16.dp)) {
            Row(
                modifier = Modifier.fillMaxWidth(),
                horizontalArrangement = Arrangement.SpaceBetween,
                verticalAlignment = Alignment.CenterVertically
            ) {
                Text(
                    text = booking.movieTitle,
                    style = MaterialTheme.typography.titleMedium,
                    fontWeight = FontWeight.Bold,
                    modifier = Modifier.weight(1f)
                )
                AssistChip(
                    onClick = { onToggleStatus(booking) },
                    label = {
                        Text(
                            text = booking.status,
                            color = if (isActive) Color(0xFF4CAF50) else Color(0xFFEF5350),
                            fontWeight = FontWeight.Bold
                        )
                    }
                )
            }
            Spacer(modifier = Modifier.height(4.dp))
            Text("Cliente: ${booking.customerName}", style = MaterialTheme.typography.bodyMedium)
            Text(
                text = "Función: ${booking.showtime} • ${booking.ticketCount} boleto(s) ${if (booking.isVip) "(VIP)" else "(Estándar)"}",
                style = MaterialTheme.typography.bodySmall
            )
            Spacer(modifier = Modifier.height(8.dp))
            Row(
                modifier = Modifier.fillMaxWidth(),
                horizontalArrangement = Arrangement.SpaceBetween,
                verticalAlignment = Alignment.CenterVertically
            ) {
                Text(
                    text = "Total: $$formattedTotal",
                    style = MaterialTheme.typography.titleMedium,
                    color = MaterialTheme.colorScheme.primary,
                    fontWeight = FontWeight.Bold
                )
                Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                    OutlinedButton(onClick = { onToggleStatus(booking) }) {
                        Text(if (isActive) "Cancelar" else "Reactivar")
                    }
                    IconButton(onClick = { onDelete(booking) }) {
                        Icon(
                            imageVector = Icons.Default.Delete,
                            contentDescription = "Eliminar reserva",
                            tint = Color(0xFFEF5350)
                        )
                    }
                }
            }
        }
    }
}

// ============================================================================
// 8. PANTALLAS DE LA APLICACIÓN (JETPACK COMPOSE)
// ============================================================================

@Composable
fun HomeScreen(
    movies: List<MovieEntity>,
    onMovieSelect: (MovieEntity) -> Unit,
    onFavoriteToggle: (MovieEntity) -> Unit,
    onAddMovieClick: () -> Unit,
    onEditMovieClick: (MovieEntity) -> Unit,
    onDeleteMovieClick: (MovieEntity) -> Unit,
    onNavigateToFavorites: () -> Unit,
    onNavigateToBookings: () -> Unit,
    onNavigateToStats: () -> Unit
) {
    var searchQuery by remember { mutableStateOf("") }

    val filteredMovies = remember(searchQuery, movies) {
        movies.filter {
            it.title.contains(searchQuery, ignoreCase = true) ||
                    it.genre.contains(searchQuery, ignoreCase = true)
        }
    }

    Scaffold(
        floatingActionButton = {
            FloatingActionButton(
                onClick = onAddMovieClick,
                containerColor = MaterialTheme.colorScheme.primary
            ) {
                Icon(Icons.Default.Add, contentDescription = "Nueva Película")
            }
        }
    ) { innerPadding ->
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(innerPadding)
        ) {
            HeaderBanner(title = "CineVerse", subtitle = "Cartelera y Gestión Local (Room)")

            Column(modifier = Modifier.padding(16.dp)) {
                OutlinedTextField(
                    value = searchQuery,
                    onValueChange = { searchQuery = it },
                    label = { Text("Buscar película o género...") },
                    modifier = Modifier.fillMaxWidth(),
                    singleLine = true
                )

                Spacer(modifier = Modifier.height(8.dp))

                Row(
                    modifier = Modifier.fillMaxWidth(),
                    horizontalArrangement = Arrangement.spacedBy(6.dp)
                ) {
                    Button(
                        onClick = onNavigateToBookings,
                        modifier = Modifier.weight(1f),
                        contentPadding = PaddingValues(horizontal = 8.dp)
                    ) {
                        Text("Reservas", maxLines = 1)
                    }
                    OutlinedButton(
                        onClick = onNavigateToFavorites,
                        modifier = Modifier.weight(1f),
                        contentPadding = PaddingValues(horizontal = 8.dp)
                    ) {
                        Text("Favoritos", maxLines = 1)
                    }
                    OutlinedButton(
                        onClick = onNavigateToStats,
                        modifier = Modifier.weight(1f),
                        contentPadding = PaddingValues(horizontal = 8.dp)
                    ) {
                        Text("Stats", maxLines = 1)
                    }
                }

                Spacer(modifier = Modifier.height(12.dp))

                LazyColumn(modifier = Modifier.fillMaxSize()) {
                    items(filteredMovies, key = { it.id }) { movie ->
                        MovieCard(
                            movie = movie,
                            onMovieClick = onMovieSelect,
                            onFavoriteToggle = onFavoriteToggle,
                            onEditClick = onEditMovieClick,
                            onDeleteClick = onDeleteMovieClick
                        )
                    }
                }
            }
        }
    }
}

@Composable
fun MovieFormScreen(
    existingMovie: MovieEntity?,
    onSaveNew: (String, String, Double, String, String, Double, String) -> Unit,
    onUpdateExisting: (MovieEntity) -> Unit,
    onCancel: () -> Unit
) {
    val isEditing = existingMovie != null

    var title by remember { mutableStateOf(existingMovie?.title ?: "") }
    var genre by remember { mutableStateOf(existingMovie?.genre ?: "") }
    var duration by remember { mutableStateOf(existingMovie?.duration ?: "") }
    var ratingText by remember { mutableStateOf(existingMovie?.rating?.toString() ?: "4.5") }
    var priceText by remember { mutableStateOf(existingMovie?.price?.toString() ?: "") }
    var showtimes by remember { mutableStateOf(existingMovie?.showtimes ?: "15:00, 18:30, 21:00") }
    var synopsis by remember { mutableStateOf(existingMovie?.synopsis ?: "") }
    var errorMessage by remember { mutableStateOf<String?>(null) }

    Column(modifier = Modifier.fillMaxSize()) {
        HeaderBanner(
            title = if (isEditing) "Editar Película" else "Registrar Película",
            subtitle = "Almacenamiento local en Room"
        )

        Column(
            modifier = Modifier
                .padding(16.dp)
                .verticalScroll(rememberScrollState()),
            verticalArrangement = Arrangement.spacedBy(10.dp)
        ) {
            OutlinedTextField(
                value = title,
                onValueChange = { title = it },
                label = { Text("Título de la película *") },
                modifier = Modifier.fillMaxWidth(),
                singleLine = true
            )
            OutlinedTextField(
                value = genre,
                onValueChange = { genre = it },
                label = { Text("Género (ej. Acción, Drama) *") },
                modifier = Modifier.fillMaxWidth(),
                singleLine = true
            )
            Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                OutlinedTextField(
                    value = duration,
                    onValueChange = { duration = it },
                    label = { Text("Duración (ej. 2h 15m) *") },
                    modifier = Modifier.weight(1f),
                    singleLine = true
                )
                OutlinedTextField(
                    value = ratingText,
                    onValueChange = { ratingText = it },
                    label = { Text("Calificación (1-5)") },
                    keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Decimal),
                    modifier = Modifier.weight(1f),
                    singleLine = true
                )
            }
            OutlinedTextField(
                value = priceText,
                onValueChange = { priceText = it },
                label = { Text("Precio del boleto ($) *") },
                keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Decimal),
                modifier = Modifier.fillMaxWidth(),
                singleLine = true
            )
            OutlinedTextField(
                value = showtimes,
                onValueChange = { showtimes = it },
                label = { Text("Horarios (separados por coma) *") },
                modifier = Modifier.fillMaxWidth(),
                singleLine = true
            )
            OutlinedTextField(
                value = synopsis,
                onValueChange = { synopsis = it },
                label = { Text("Sinopsis *") },
                modifier = Modifier.fillMaxWidth(),
                minLines = 3
            )

            if (errorMessage != null) {
                Text(
                    text = errorMessage!!,
                    color = MaterialTheme.colorScheme.error,
                    style = MaterialTheme.typography.bodySmall
                )
            }

            Spacer(modifier = Modifier.height(8.dp))

            Button(
                onClick = {
                    val price = priceText.toDoubleOrNull()
                    val rating = ratingText.toDoubleOrNull() ?: 4.5

                    when {
                        title.isBlank() || genre.isBlank() || duration.isBlank() || synopsis.isBlank() || showtimes.isBlank() -> {
                            errorMessage = "Todos los campos marcados con * son obligatorios."
                        }
                        (price == null || price <= 0.0) -> {
                            errorMessage = "Ingresa un precio válido mayor a 0."
                        }
                        rating !in 0.0..5.0 -> {
                            errorMessage = "La calificación debe estar entre 0.0 y 5.0."
                        }
                        else -> {
                            if (existingMovie != null) {
                                onUpdateExisting(
                                    existingMovie.copy(
                                        title = title.trim(),
                                        genre = genre.trim(),
                                        duration = duration.trim(),
                                        rating = rating,
                                        price = price,
                                        showtimes = showtimes.trim(),
                                        synopsis = synopsis.trim()
                                    )
                                )
                            } else {
                                onSaveNew(
                                    title.trim(),
                                    genre.trim(),
                                    rating,
                                    duration.trim(),
                                    synopsis.trim(),
                                    price,
                                    showtimes.trim()
                                )
                            }
                        }
                    }
                },
                modifier = Modifier.fillMaxWidth()
            ) {
                Text(if (isEditing) "Guardar Cambios" else "Registrar en Cartelera")
            }

            OutlinedButton(
                onClick = onCancel,
                modifier = Modifier.fillMaxWidth()
            ) {
                Text("Cancelar")
            }
        }
    }
}

@Composable
fun BookingScreen(
    movie: MovieEntity,
    onConfirmBooking: (MovieEntity, String, String, Int, Boolean, Double) -> Unit,
    onBack: () -> Unit
) {
    val showtimesList = remember(movie) { movie.getShowtimesList() }
    var customerName by remember { mutableStateOf("") }
    var selectedShowtime by remember { mutableStateOf(showtimesList.firstOrNull() ?: "18:00") }
    var ticketCount by remember { mutableIntStateOf(1) }
    var isVip by remember { mutableStateOf(value = false) }
    var errorMessage by remember { mutableStateOf<String?>(null) }

    val vipSurcharge = if (isVip) 5.0 else 0.0
    val totalPrice = (movie.price + vipSurcharge) * ticketCount
    val formattedTotal = String.format(Locale.US, "%.2f", totalPrice)

    Column(modifier = Modifier.fillMaxSize()) {
        HeaderBanner(title = "Reservar Entradas", subtitle = movie.title)

        Column(
            modifier = Modifier
                .padding(16.dp)
                .fillMaxSize()
                .verticalScroll(rememberScrollState()),
            verticalArrangement = Arrangement.SpaceBetween
        ) {
            Column {
                Text(text = "Sinopsis", style = MaterialTheme.typography.titleMedium)
                Text(text = movie.synopsis, style = MaterialTheme.typography.bodyMedium)

                Spacer(modifier = Modifier.height(14.dp))

                OutlinedTextField(
                    value = customerName,
                    onValueChange = { customerName = it },
                    label = { Text("Nombre del cliente *") },
                    modifier = Modifier.fillMaxWidth(),
                    singleLine = true
                )

                Spacer(modifier = Modifier.height(14.dp))

                Text(text = "Selecciona Horario", style = MaterialTheme.typography.titleMedium)
                Spacer(modifier = Modifier.height(6.dp))

                LazyRow(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                    items(showtimesList) { time ->
                        FilterChip(
                            selected = (selectedShowtime == time),
                            onClick = { selectedShowtime = time },
                            label = { Text(time) }
                        )
                    }
                }

                Spacer(modifier = Modifier.height(14.dp))

                Text(text = "Cantidad de Boletos", style = MaterialTheme.typography.titleMedium)
                Row(
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.spacedBy(16.dp)
                ) {
                    Button(
                        onClick = { if (ticketCount > 1) ticketCount-- },
                        enabled = ticketCount > 1
                    ) { Text("-") }

                    Text(text = ticketCount.toString(), style = MaterialTheme.typography.titleLarge)

                    Button(
                        onClick = { if (ticketCount < 10) ticketCount++ },
                        enabled = ticketCount < 10
                    ) { Text("+") }
                }

                Spacer(modifier = Modifier.height(12.dp))

                Row(
                    verticalAlignment = Alignment.CenterVertically,
                    horizontalArrangement = Arrangement.SpaceBetween,
                    modifier = Modifier.fillMaxWidth()
                ) {
                    Text("Experiencia VIP (+ $5.00/boleto)")
                    Switch(checked = isVip, onCheckedChange = { isVip = it })
                }

                if (errorMessage != null) {
                    Text(
                        text = errorMessage!!,
                        color = MaterialTheme.colorScheme.error,
                        style = MaterialTheme.typography.bodySmall
                    )
                }
            }

            Spacer(modifier = Modifier.height(16.dp))

            Card(
                modifier = Modifier.fillMaxWidth(),
                colors = CardDefaults.cardColors(containerColor = MaterialTheme.colorScheme.surface)
            ) {
                Column(modifier = Modifier.padding(16.dp)) {
                    Text(
                        text = "Total a pagar: $$formattedTotal",
                        style = MaterialTheme.typography.titleLarge,
                        color = MaterialTheme.colorScheme.primary
                    )
                    Spacer(modifier = Modifier.height(8.dp))
                    Button(
                        onClick = {
                            if (customerName.isBlank()) {
                                errorMessage = "Por favor ingresa el nombre del cliente."
                            } else {
                                onConfirmBooking(
                                    movie,
                                    customerName.trim(),
                                    selectedShowtime,
                                    ticketCount,
                                    isVip,
                                    totalPrice
                                )
                            }
                        },
                        modifier = Modifier.fillMaxWidth()
                    ) {
                        Text("Confirmar y Guardar Reserva")
                    }
                    Spacer(modifier = Modifier.height(6.dp))
                    OutlinedButton(
                        onClick = onBack,
                        modifier = Modifier.fillMaxWidth()
                    ) {
                        Text("Volver")
                    }
                }
            }
        }
    }
}

@Composable
fun BookingsHistoryScreen(
    bookings: List<BookingEntity>,
    onToggleStatus: (BookingEntity) -> Unit,
    onDeleteBooking: (BookingEntity) -> Unit,
    onBack: () -> Unit
) {
    var selectedFilter by remember { mutableStateOf("Todas") }
    val filters = listOf("Todas", "Activa", "Cancelada")

    val filteredBookings = remember(bookings, selectedFilter) {
        if (selectedFilter == "Todas") bookings
        else bookings.filter { it.status == selectedFilter }
    }

    Column(modifier = Modifier.fillMaxSize()) {
        HeaderBanner(
            title = "Historial de Reservas",
            subtitle = "Registros almacenados en Room"
        )

        Column(modifier = Modifier.padding(16.dp)) {
            Button(onClick = onBack, modifier = Modifier.fillMaxWidth()) {
                Text("Volver a la cartelera")
            }

            Spacer(modifier = Modifier.height(10.dp))

            Row(horizontalArrangement = Arrangement.spacedBy(8.dp)) {
                filters.forEach { filter ->
                    FilterChip(
                        selected = selectedFilter == filter,
                        onClick = { selectedFilter = filter },
                        label = { Text(filter) }
                    )
                }
            }

            Spacer(modifier = Modifier.height(10.dp))

            if (filteredBookings.isEmpty()) {
                Box(modifier = Modifier.fillMaxSize(), contentAlignment = Alignment.Center) {
                    Text("No hay reservas registradas en esta categoría.")
                }
            } else {
                LazyColumn {
                    items(filteredBookings, key = { it.id }) { booking ->
                        BookingCard(
                            booking = booking,
                            onToggleStatus = onToggleStatus,
                            onDelete = onDeleteBooking
                        )
                    }
                }
            }
        }
    }
}

@Composable
fun FavoritesScreen(
    favoriteMovies: List<MovieEntity>,
    onMovieSelect: (MovieEntity) -> Unit,
    onFavoriteToggle: (MovieEntity) -> Unit,
    onBack: () -> Unit
) {
    Column(modifier = Modifier.fillMaxSize()) {
        HeaderBanner(title = "Mis Favoritos", subtitle = "Guardados localmente en Room")

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
                    items(favoriteMovies, key = { it.id }) { movie ->
                        MovieCard(
                            movie = movie,
                            onMovieClick = onMovieSelect,
                            onFavoriteToggle = onFavoriteToggle
                        )
                    }
                }
            }
        }
    }
}

@Composable
fun StatsScreen(
    stats: CinemaStats,
    onBack: () -> Unit
) {
    val formattedRevenue = String.format(Locale.US, "%.2f", stats.totalRevenue)

    Column(modifier = Modifier.fillMaxSize()) {
        HeaderBanner(
            title = "Estadísticas del Cine",
            subtitle = "Resumen financiero y operativo en tiempo real"
        )

        Column(
            modifier = Modifier.padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(12.dp)
        ) {
            StatItemCard(title = "Ingresos Totales (Reservas Activas)", value = "$$formattedRevenue", highlight = true)
            StatItemCard(title = "Boletos Vendidos Activos", value = "${stats.totalTicketsSold} boletos")
            StatItemCard(title = "Películas en Cartelera", value = "${stats.totalMovies} títulos")
            StatItemCard(title = "Reservas Activas vs Canceladas", value = "${stats.activeBookings} activas / ${stats.canceledBookings} canceladas")

            Spacer(modifier = Modifier.height(12.dp))

            Button(onClick = onBack, modifier = Modifier.fillMaxWidth()) {
                Text("Volver a la cartelera")
            }
        }
    }
}

@Composable
private fun StatItemCard(title: String, value: String, highlight: Boolean = false) {
    Card(
        modifier = Modifier.fillMaxWidth(),
        colors = CardDefaults.cardColors(containerColor = MaterialTheme.colorScheme.surface)
    ) {
        Column(modifier = Modifier.padding(16.dp)) {
            Text(text = title, style = MaterialTheme.typography.bodyMedium)
            Spacer(modifier = Modifier.height(4.dp))
            Text(
                text = value,
                style = MaterialTheme.typography.titleLarge,
                fontWeight = FontWeight.Bold,
                color = if (highlight) MaterialTheme.colorScheme.primary else MaterialTheme.colorScheme.onSurface
            )
        }
    }
}

// ============================================================================
// 9. ACTIVIDAD PRINCIPAL Y NAVEGACIÓN (MainActivity)
// ============================================================================

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val database = CineDatabase.getDatabase(applicationContext)
        val repository = CineRepository(database.movieDao(), database.bookingDao())
        val viewModelFactory = CineViewModelFactory(repository)

        setContent {
            CineVerseTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    val viewModel: CineViewModel = viewModel(factory = viewModelFactory)
                    CineApp(viewModel = viewModel)
                }
            }
        }
    }
}

@Composable
fun CineApp(viewModel: CineViewModel) {
    val navController = rememberNavController()

    val movies by viewModel.movies.collectAsStateWithLifecycle()
    val bookings by viewModel.bookings.collectAsStateWithLifecycle()
    val stats by viewModel.stats.collectAsStateWithLifecycle()

    NavHost(navController = navController, startDestination = "home") {

        composable("home") {
            HomeScreen(
                movies = movies,
                onMovieSelect = { movie -> navController.navigate("booking/${movie.id}") },
                onFavoriteToggle = { movie -> viewModel.toggleFavorite(movie) },
                onAddMovieClick = { navController.navigate("movie_form/-1") },
                onEditMovieClick = { movie -> navController.navigate("movie_form/${movie.id}") },
                onDeleteMovieClick = { movie -> viewModel.deleteMovie(movie) },
                onNavigateToFavorites = { navController.navigate("favorites") },
                onNavigateToBookings = { navController.navigate("bookings") },
                onNavigateToStats = { navController.navigate("stats") }
            )
        }

        composable(
            route = "movie_form/{movieId}",
            arguments = listOf(navArgument("movieId") { type = NavType.IntType })
        ) { backStackEntry ->
            val movieId = backStackEntry.arguments?.getInt("movieId") ?: -1
            val existingMovie = movies.firstOrNull { it.id == movieId }

            MovieFormScreen(
                existingMovie = existingMovie,
                onSaveNew = { title, genre, rating, duration, synopsis, price, showtimes ->
                    viewModel.addMovie(title, genre, rating, duration, synopsis, price, showtimes)
                    navController.popBackStack()
                },
                onUpdateExisting = { updatedMovie ->
                    viewModel.updateMovie(updatedMovie)
                    navController.popBackStack()
                },
                onCancel = { navController.popBackStack() }
            )
        }

        composable(
            route = "booking/{movieId}",
            arguments = listOf(navArgument("movieId") { type = NavType.IntType })
        ) { backStackEntry ->
            val movieId = backStackEntry.arguments?.getInt("movieId") ?: -1
            val selectedMovie = movies.firstOrNull { it.id == movieId }

            selectedMovie?.let { movie ->
                BookingScreen(
                    movie = movie,
                    onConfirmBooking = { targetMovie, customer, time, count, vip, total ->
                        viewModel.createBooking(targetMovie, customer, time, count, vip, total)
                        navController.navigate("bookings") {
                            popUpTo("home")
                        }
                    },
                    onBack = { navController.popBackStack() }
                )
            }
        }

        composable("bookings") {
            BookingsHistoryScreen(
                bookings = bookings,
                onToggleStatus = { viewModel.toggleBookingStatus(it) },
                onDeleteBooking = { viewModel.deleteBooking(it) },
                onBack = { navController.popBackStack() }
            )
        }

        composable("favorites") {
            FavoritesScreen(
                favoriteMovies = movies.filter { it.isFavorite },
                onMovieSelect = { movie -> navController.navigate("booking/${movie.id}") },
                onFavoriteToggle = { viewModel.toggleFavorite(it) },
                onBack = { navController.popBackStack() }
            )
        }

        composable("stats") {
            StatsScreen(
                stats = stats,
                onBack = { navController.popBackStack() }
            )
        }
    }
}
