<script lang="ts">
  import type { Movie } from '$lib/types';

  // Props con Svelte 5: sistema de tipos explícito y callbacks en lugar de eventos
  let { 
    movie,
    showActions = true,
    ondelete,
    onedit,
    onrate
  }: {
    movie: Movie;
    showActions?: boolean;
    ondelete?: (id: string) => void;
    onedit?: (movie: Movie) => void;
    onrate?: (movie: Movie, rating: number) => void;
  } = $props();

  const ratingValues = [1, 2, 3, 4, 5];

  // Handlers: ejecutan callbacks del padre directamente
  function handleDelete() {
    ondelete?.(movie.id);
  }

  function handleEdit() {
    onedit?.(movie);
  }
</script>

<!-- Componente reutilizable: tarjeta para mostrar información de una película -->
<article class="flex flex-col overflow-hidden rounded-lg border border-slate-200 bg-white shadow-sm">
  {#if movie.posterUrl}
    <div class="flex h-48 items-center justify-center bg-slate-100">
      <img
        alt={`Póster de ${movie.title}`}
        class="max-h-full max-w-full object-contain"
        src={movie.posterUrl}
        loading="lazy"
      />
    </div>
  {/if}

  <div class="flex flex-1 flex-col gap-3 p-4">
    <header>
      <h3 class="text-lg font-semibold text-slate-900">{movie.title}</h3>
      <p class="text-sm text-slate-600">Dirigida por {movie.director}</p>
    </header>

    <div class="mt-auto text-sm text-slate-500">
      {#if movie.year}
        <span>Año: {movie.year}</span>
      {/if}
    </div>

    {#if showActions}
      <div class="flex items-center gap-1" aria-label={`Puntuación: ${movie.rating ?? 0} de 5`}>
        {#each ratingValues as rating}
          <button
            type="button"
            class={`text-2xl leading-none transition hover:scale-110 ${
              rating <= (movie.rating ?? 0) ? 'text-yellow-400' : 'text-slate-300'
            }`}
            onclick={() => onrate?.(movie, rating)}
            aria-label={`Puntuar con ${rating} de 5`}
            aria-pressed={rating === (movie.rating ?? 0)}
          >
            {rating <= (movie.rating ?? 0) ? '★' : '☆'}
          </button>
        {/each}
      </div>

      <div class="mt-3 flex flex-col gap-2 sm:flex-row">
        <button
          type="button"
          class="w-full rounded border border-slate-300 px-3 py-2 text-slate-700 transition hover:bg-slate-50"
          onclick={handleEdit}
        >
          Editar
        </button>
        <button
          type="button"
          class="w-full rounded border border-red-500 px-3 py-2 text-red-600 transition hover:bg-red-50"
          onclick={handleDelete}
        >
          Eliminar
        </button>
      </div>
    {/if}
  </div>
</article>
