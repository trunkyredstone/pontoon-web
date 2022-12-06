<script>
    import {Button, Card, CardBody, Col, Container, Input, Row} from "sveltestrap";
    import {fade} from "svelte/transition"

    let userNumbers = [0, 0, 0, 0, 0, 0];
    let generatedNumbers = [];
    let goodGeneratedNumbers = [false, false, false, false, false, false]
    let guessed = false;
    let processed = false;
    let matches = 0;

    function print() {
        console.log(userNumbers);
        guessed = true;

        addNumber()
    }

    function addNumber() {
        console.log("Adding number");

        generatedNumbers.push(Math.floor(Math.random() * 59) + 1)
        generatedNumbers = generatedNumbers;

        let genLength = generatedNumbers.length;
        if (genLength < 6) {
            setTimeout(addNumber, 1000);
        } else if (genLength === 6) {
            setTimeout(checkNumbers, 1000);
        }
    }

    function checkNumbers() {
        console.log("Checking numbers");

        for (let i = 0; i < 6; i++) {
            for (let j = 0; j < 6; j++) {
                if (userNumbers[i] === generatedNumbers[j]) {
                    goodGeneratedNumbers[j] = true;
                    matches += 1;
                }
            }
        }

        setTimeout(finalise, 1000);
    }

    function finalise() {
        processed = true;
    }

    function restart() {
        processed = false;
        guessed = false;
        generatedNumbers = [];
        goodGeneratedNumbers = [false, false, false, false, false, false];
    }
</script>

<Container>
    <Row>
        <Col class="pt-3 text-center">
            <h1>National Lottery</h1>
        </Col>
    </Row>
    <hr>
    <Card>
        <CardBody>
            <Row class="text-center">
                {#each userNumbers as number, i}
                    <Col>
                        <Input type="number" min="1" max="59" bind:value={userNumbers[i]}></Input>
                    </Col>
                {/each}
            </Row>
        </CardBody>
    </Card>
    <hr>
    <Card class="text-center">
        <CardBody>
            <Row>
                {#if guessed}
                    {#each generatedNumbers as number, i}
                        <Col>
                            <div transition:fade={{duration: 1000}} class={goodGeneratedNumbers[i] ? "text-success" : ""}>
                                {number}
                            </div>
                        </Col>
                    {/each}
                {:else}
                    <Col>
                        <Button class="mt-1" on:click={print}>Go!</Button>
                    </Col>
                {/if}
            </Row>
        </CardBody>
    </Card>
    {#if processed}
        <hr>
        <div transition:fade class="text-center">
            <p>
                {#if matches === 0}
                    You didn't guess any.
                {:else}
                    Congratulations! You guessed {matches} correctly.
                {/if}
            </p>
            <Button on:click={restart}>
                Go again
            </Button>
        </div>
    {/if}
</Container>