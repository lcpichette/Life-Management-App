<script lang='ts'>
    export let onClick: any;
</script>

<div class='fab'>
    <button class='fab-button' on:click={onClick}>+</button>
</div>

<style lang='scss'>
    @import '../styles/colors.scss';
    @import '../styles/boxShadows.scss';

    // Settings
    $size: 38px;
    $fab-bg-color: $secondary-bg;
    $fab-text-color: $secondary-text;


    // Styles
    .fab {
        display: flex;
        flex-direction: row-reverse;

        &-button {
            cursor: pointer;

            border: none;
            border-radius: calc($size / 2);

            height: $size;
            width: $size;

            background-color: $fab-bg-color;
            color: $fab-text-color;
            font-size: 1.5rem;

            box-shadow: $bs-8;
            transition: box-shadow 0.3s cubic-bezier(.25,.8,.25,1);

            &:hover {
                box-shadow: $bs-12;
            }
        }
    }
</style>