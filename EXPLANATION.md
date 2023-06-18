# Frontend Explanation

1. Debounce Search
``` vue.js
methods: {
  /*
    Separate button and actual call to prevent
    2x or multiple API calls at a time.
  */

  search() {
    // this is call when user click search Button
    this.$router.push({query: { 'searchQuery' : this.searchQuery}})
  },
  performSearch() {
    this.$emit('search', this.searchQuery);
  },
  handleInputChange(param) {
    if (param) {
      this.searchQuery = '';
    }

    /*
      Debouncing search,
      create responsive search,
      preventing useless API calls
    */

    // Debounce the input change to delay API call
    if (!this.debounceQuery) {
      this.debounceQuery = debounce(() => {
        this.performSearch();
      }, 500); // Adjust the debounce delay as per your requirements
    }
    this.debounceQuery();
  },
},
```

Use debounce search for performance, make sure API calls 1 at a time but still responsive to the user. When user type something and then there's 500ms delay (timeout) for the API to calls. This is very good for performance and not wasting API calls.

2. Tailwind Theming
``` css
/* Define custom utility classes */
@layer utilities {
  .bg-1 {
    background-color: #E9E3D3;
  }

  .bg-1-darken {
    background-color: #D0CABA;
  }

  .bg-2 {
    background-color: #FFA800;
  }

  .bg-3 {
    background-color: #C29687;
  }

  .bg-3-darken {
    background-color: #A97D6E;
  }
}

body {
  @apply bg-1
}

@layer components {
  /* button style */
  .btn {
    @apply border-2 border-black px-4 py-2 text-sm;
  }
  .btn-md {
    @apply border-2 border-black px-3.5 py-1.5 text-sm;
  }
  .btn-sm {
    @apply border-2 border-black px-3.5 py-1.5 text-xs;
  }

  /* card style */
  .card {
    @apply w-full overflow-hidden border-2 border-black
  }
}
```

Using Tailwind CSS theming for readable and reusable code. Make Tailwind CSS breakpoints for responsive design also.

3. Responsiveness
``` css
/* box style */
.box-page {
  @apply relative items-center px-2 py-2.5 ring-1 ring-inset ring-black text-gray-700 w-11 h-11
}
.box-page-md {
  @apply relative items-center px-1 py-1.5 ring-1 ring-inset ring-black text-gray-700 w-9 h-9 text-sm
}
.box-page-sm {
  @apply relative items-center px-1 py-1.5 ring-1 ring-inset ring-black text-gray-700 w-8 h-8 text-sm
}
.box-page-xs {
  @apply relative items-center px-1 py-2 ring-1 ring-inset ring-black text-gray-700 w-9 h-9 text-sm
}
```

Using responsive design so user can use application for any devices.

<div align="center">
  <img src="https://github.com/bangkitdc/frontend-redcomm-intern/blob/master/assets/laptop.png" width="350">
  <img src="https://github.com/bangkitdc/frontend-redcomm-intern/blob/master/assets/tablet.png" width="250">
  <img src="https://github.com/bangkitdc/frontend-redcomm-intern/blob/master/assets/iphone.png" width="150">
  <img src="https://github.com/bangkitdc/frontend-redcomm-intern/blob/master/assets/android.png" width="150">
</div>
