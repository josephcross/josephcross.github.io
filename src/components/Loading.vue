<template>
    <div class="loading">
        <div class="bounce1"></div>
        <div class="bounce2"></div>
        <div class="bounce3"></div>
    </div>
</template>

<script>
export default {
  name: 'Loading',
};
</script>

<style lang="scss">
    .loading {
        margin-top: 40px;
        padding: 2rem;
        height: 96px;
        display: flex;
        align-items: center;
        width: auto;
        text-align: center;

        div {
            width: 20px;
            height: 20px;
            background-color: #2c3e50;
            margin: 0 8px;

            border-radius: 100%;
            display: inline-block;
            -webkit-animation: sk-bouncedelay 1.4s infinite ease-in-out both;
            animation: sk-bouncedelay 1.4s infinite ease-in-out both;
        }
    }

    @media screen and ( max-width: 768px ) {
        .loading {
            height: 72px;
            margin-top: 20px;

            div {
                width: 16px;
                height: 16px;
                margin: 0 7px;
            }
        }
    }

    .loading .bounce1 {
      -webkit-animation-delay: -0.32s;
      animation-delay: -0.32s;
    }

    .loading .bounce2 {
      -webkit-animation-delay: -0.16s;
      animation-delay: -0.16s;
    }

    @-webkit-keyframes sk-bouncedelay {
      0%, 80%, 100% { -webkit-transform: scale(0) }
      40% { -webkit-transform: scale(1.0) }
    }

    @keyframes sk-bouncedelay {
      0%, 80%, 100% {
        -webkit-transform: scale(0);
        transform: scale(0);
      } 40% {
        -webkit-transform: scale(1.0);
        transform: scale(1.0);
      }
    }

</style>
