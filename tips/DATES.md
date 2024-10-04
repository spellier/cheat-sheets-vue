# Dates

<!-- TOC -->
* [Dates](#dates)
  * [Résumé](#résumé)
  * [Lecture](#lecture)
    * [Date seule](#date-seule)
    * [Date-time locale](#date-time-locale)
    * [Date-time localisation forcée](#date-time-localisation-forcée)
  * [Manipulation](#manipulation)
  * [Récupération de la timezone locale](#récupération-de-la-timezone-locale)
  * [Pour aller plus loin](#pour-aller-plus-loin)
<!-- TOC -->

## Résumé

Il existe 3 types de dates :
- La date seule indépendante de toute notion de temps (Date d'anniversaire).
- La date-time locale, qui correspond à un événement dans le fuseau de l'utilisateur (Date d'un événement sportif).
- La date-time avec localisation forcée, qui correspond à un événement dans un fuseau défini.

Pour la gestion des dates, nous allons selon le contexte, soit utiliser l'objet **Date**, soit la librairie **dayjs**.

| Type                          | Lecture | Formulaire            | Écriture              | Manipulation |
|-------------------------------|---------|-----------------------|-----------------------|--------------|
| Date seule                    | Date    | string (DateTime ISO) | string (Date ISO)     | dayJS        |
| Date-time locale              | Date    | string (DateTime ISO) | string (DateTime ISO) | dayJS        |
| Date-time localisation forcée | dayJS   | string (DateTime ISO) | string (DateTime ISO) | dayJS        |

*DateTime ISO = YYYY-MM-DDTHH:mm:ssZ*  
*Date ISO = YYYY-MM-DD*


Imaginons les données suivantes :

```typescript
interface DemoDate {
  birthDate: string;
  startingDate: string;
  landingDate: string;
  matchDate: string;
}

const demoDate = {
  birthDate: "1980-03-15",
  landingDate: "2024-03-16T02:00:00.000Z",
  matchDate: "2024-03-17T04:00:00.000Z",
  startingDate: "2024-03-15T11:00:00.000Z"
};
```

## Lecture

### Date seule

```vue
<script setup lang="ts">
  import { ref, computed } from 'vue'

  const demoDate = ref({
  birthDate: "1980-03-15",
  landingDate: "2024-03-16T02:00:00.000Z",
  matchDate: "2024-03-17T04:00:00.000Z",
  startingDate: "2024-03-15T11:00:00.000Z"
})

  const formattedBirthDate = computed(() => {
  const date = new Date(demoDate.value.birthDate)
  return new Intl.DateTimeFormat(undefined, {
    weekday: 'long', 
    year: 'numeric', 
    month: 'long', 
    day: 'numeric'
}).format(date)
})
</script>

<template>
  <h1>Date de naissance : {{ formattedBirthDate }}</h1>
</template>
```
>Résultat > Date de naissance : samedi 15 mars 1980

### Date-time locale

```vue
<script setup lang="ts">
  import { ref, computed } from 'vue'

  const demoDate = ref({
    // ... comme précédemment
  })

  const formattedMatchDate = computed(() => {
    const date = new Date(demoDate.value.matchDate)
    return new Intl.DateTimeFormat(undefined, {
      year: 'numeric',
      month: '2-digit',
      day: '2-digit',
      hour: '2-digit',
      minute: '2-digit'
    }).format(date)
  })
</script>

<template>
  <h1>Superbowl : {{ formattedMatchDate }}</h1>
</template>

```

>Résultat (Europe/Paris) > Superbowl : 17/03/2024 05:00  
>Résultat (America/Phoenix) > Superbowl : 16/03/2024 21:00

### Date-time localisation forcée

Récupération de la date dans le fuseau demandé
```vue
<script setup lang="ts">
  import { ref, computed } from 'vue'
  import dayjs from 'dayjs'
  import timezone from 'dayjs/plugin/timezone'
  import utc from 'dayjs/plugin/utc'

  dayjs.extend(utc)
  dayjs.extend(timezone)

  const demoDate = ref({
    // ... comme précédemment
  })

  const startingTime = computed(() =>
          dayjs(demoDate.value.startingDate)
                  .tz('Europe/Paris')
                  .format('DD/MM/YYYY HH:mm')
  )

  const landingTime = computed(() =>
          dayjs(demoDate.value.landingDate)
                  .tz('America/Los_Angeles')
                  .format('DD/MM/YYYY HH:mm')
  )
</script>

<template>
  <h1>Départ Paris : {{ startingTime }}</h1>
  <h1>Arrivée Las Vegas : {{ landingTime }}</h1>
</template>

```

>Résultat (Europe/Paris) > Départ Paris : 15/03/2024 12:00  
>Résultat (Europe/Paris) > Arrivée Las Vegas : 15/03/2024 19:00  
>Résultat (America/Phoenix) > Départ Paris : 15/03/2024 12:00  
>Résultat (America/Phoenix) > Arrivée Las Vegas : 15/03/2024 19:00

## Formulaire

```vue
<script setup lang="ts">
import { ref, reactive } from 'vue'
import dayjs from 'dayjs'
import timezone from 'dayjs/plugin/timezone'
import utc from 'dayjs/plugin/utc'

dayjs.extend(utc)
dayjs.extend(timezone)

interface DateForm {
  birthDate: string;
  startingDate: string;
  startingTime: string;
  landingDate: string;
  landingTime: string;
  matchDate: string;
  matchTime: string;
}

const demoDate = ref({
  // ... comme précédemment
})

const form = reactive<DateForm>({
  birthDate: new Date(demoDate.value.birthDate).toISOString(),
  startingDate: new Date(demoDate.value.startingDate).toISOString(),
  startingTime: dayjs(demoDate.value.startingDate).tz('Europe/Paris').format('HH:mm'),
  landingDate: new Date(demoDate.value.landingDate).toISOString(),
  landingTime: dayjs(demoDate.value.landingDate).tz('America/Los_Angeles').format('HH:mm'),
  matchDate: new Date(demoDate.value.matchDate).toISOString(),
  matchTime: new Date(demoDate.value.matchDate).toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })
})

const transformFormToDateIso = (date: string, time: string, timezone?: string): string => {
  let dayjsDate = dayjs(date)
  if (timezone) {
    dayjsDate = dayjsDate.tz(timezone, true)
  }
  const [hours, minutes] = time.split(':')
  return dayjsDate.hour(parseInt(hours)).minute(parseInt(minutes)).toISOString()
}

const saveDate = () => {
  const updatedDates = {
    birthDate: new Date(form.birthDate).toISOString().split('T')[0],
    startingDate: transformFormToDateIso(form.startingDate, form.startingTime, 'Europe/Paris'),
    landingDate: transformFormToDateIso(form.landingDate, form.landingTime, 'America/Los_Angeles'),
    matchDate: new Date(form.matchDate).toISOString()
  }
  
  console.log(updatedDates)
}
</script>

<template>
  <form @submit.prevent="saveDate">
    <div class="form-group">
      <label for="birthDate">Date anniversaire</label>
      <input 
        type="date" 
        id="birthDate" 
        v-model="form.birthDate"
        required
      >
    </div>

    <div class="form-group">
      <label for="startingDate">Départ Paris</label>
      <input 
        type="date" 
        id="startingDate" 
        v-model="form.startingDate"
      >
      <input 
        type="time" 
        id="startingTime" 
        v-model="form.startingTime"
      >
    </div>

    <div class="form-group">
      <label for="landingDate">Arrivée Las Vegas</label>
      <input 
        type="date" 
        id="landingDate" 
        v-model="form.landingDate"
      >
      <input 
        type="time" 
        id="landingTime" 
        v-model="form.landingTime"
      >
    </div>

    <div class="form-group">
      <label for="matchDate">Superbowl</label>
      <input 
        type="date" 
        id="matchDate" 
        v-model="form.matchDate"
      >
      <input 
        type="time" 
        id="matchTime" 
        v-model="form.matchTime"
      >
    </div>

    <button type="submit">Valider</button>
  </form>
</template>
```

## Écriture

### Date seule

```typescript
const newBirthDate = new Date(form.birthDate).toISOString().split('T')[0]
```
> Résultat > Anniversaire ISO : 1980-03-16

### Date-time locale

```typescript
const matchDate = new Date(form.matchDate + 'T' + form.matchTime).toISOString()
```
> Résultat > Superbowl ISO : 2024-03-17T05:00:00.000Z

### Date-time localisation forcée

```typescript
const startingDate = transformFormToDateIso(form.startingDate, form.startingTime, 'Europe/Paris')
const landingDate = transformFormToDateIso(form.landingDate, form.landingTime, 'America/Los_Angeles')
```
> Résultat > Départ Paris ISO : 2024-03-15T12:00:00.000Z  
> Résultat > Arrivée Vegas ISO : 2024-03-16T03:00:00.000Z


## Manipulation

Il est possible de manipuler les dates avec *Date* et *dayjs*.  
Il est cependant **vivement recommandé de n'utiliser que dayjs**, l'objet Date étant peu intuitif.

```vue
<script setup lang="ts">
import { computed } from 'vue'
import dayjs from 'dayjs'

const birthDate = dayjs("1980-03-15")

const dayAfterBirth = computed(() => 
  birthDate.add(1, 'day').format('YYYY-MM-DD')
)

const startingDateParis = computed(() => {
  const date = dayjs("2024-03-15T11:00:00.000Z")
  return date.tz('Europe/Paris').add(3, 'hour').format('YYYY-MM-DDTHH:mm')
})
</script>

<template>
  <div>
    <p>Jour après la naissance : {{ dayAfterBirth }}</p>
    <p>Départ Paris +3h : {{ startingDateParis }}</p>
  </div>
</template>
```

## Récupération de la timezone locale

Les navigateurs fournissent les notions de timezones et d'offset.
Ces notions se basent sur le système hôte du navigateur (Windows) pour fournir cette information.

```vue
<script setup lang="ts">
import { ref } from 'vue'
import dayjs from 'dayjs'

const timeZoneIntl = ref(new Intl.DateTimeFormat().resolvedOptions().timeZone)
const timeZoneDayJS = ref(dayjs.tz.guess())
        </script>

        <template>
        <div>
                <p>Timezone (Intl) : {{ timeZoneIntl }}</p>
<p>Timezone (dayjs) : {{ timeZoneDayJS }}</p>
</div>
</template>
```

## Pour aller plus loin

[MDN Web Docs](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Date)
Pour plus d'informations sur les capacités de dayJS, aller voir la [Doc officielle](https://day.js.org/docs/en/installation/installation).
