<template>
  <div class="min-h-screen flex flex-col">
    <header>
      <div class="border-b-2 border-black flex flex-1">
        <div class="px-8 max-md:px-6 max-xs:px-4 border-r-2 border-black"></div>
          <div class="border-r-2 border-black">
            <nav class="py-6 px-8 max-md:py-[18px] max-md:px-6 max-xs:p-4 flex justify-between">
              <NuxtLink to="/" class="font-bold text-xl max-md:text-lg max-xs:text-base underline underline-offset-8 max-xs:underline-offset-4">Nature Social</NuxtLink>
            </nav>
          </div>
      </div>
      <div class="bg-2 border-b-2 border-black flex justify-between">
        <div class="py-2 px-8 max-md:px-6 max-xs:px-4 border-r-2 border-black"></div>
        <div class="py-2 px-8 max-md:px-6 max-xs:px-4 border-l-2 border-black"></div>
      </div>
    </header>

    <!-- output page content -->
    <slot/>

    <footer>
      <div class="bg-2 border-t-2 border-black flex justify-between">
        <div class="py-2 px-8 max-md:px-6 max-xs:px-4 border-r-2 border-black"></div>
        <div class="py-2 px-8 max-md:px-6 max-xs:px-4 border-l-2 border-black"></div>
      </div>
      <div class="border-t-2 border-black flex justify-between">
        <div class="p-6 max-md:p-5 max-sm:p-4 flex justify-between">
          <div>
            <p class="text-base max-md:text-sm">
              © 2023, bangkitdc.
            </p>
          </div>
        </div>
        <div class="py-2 px-8 max-md:px-6 max-xs:px-4 border-l-2 border-black">
      </div>
      </div>
    </footer>
  </div>
</template>

<style scoped>

</style>
