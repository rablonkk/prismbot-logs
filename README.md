# Português - Brasil 

## PrismBot-logs
este é um repositório feito para mostrar as alterações feitas no código fonte do [PrismBot](https://prismbot.site/)

## Lista de atualizações/correções:
#### Changelogs da atualização 3.0.0 até 7.0.0 (Bot)
Devido a quantidade de commits realizados no repositório oficial do PrismBot, a lista de alterações será resumida.

* O suporte oficial ao Spotify foi removido devido erros presentes na lib `spotify-url-info`.
* O termo de serviço e de privacidade foi refeito.
* Foi adicionado um comando, o commando `/help`.
* Foram corrigidos erros ao reproduzir músicas do YouTube (principalmente playlists).
* Foram feitas melhorias visuais no código.
* Foram feitas melhorias no sistema de conexão do bot.
* Ouve algumas alterações no código fonte, para obter uma máxima performance.
* O suporte ao soundcloud foi oficialmente adicionado.
* O código responsável pelo processamento da stream foi refeito, para que o mesmo pudesse suportar música vindas da plataforma do soundcloud.
* O design do comando player e now playing foram redefinidos.
* Foram feitas correções de cálculo no comando `/queue`.

alterações feitas no código fonte:
```js
    // correção feita no comando queue;
    let queueMax = Math.ceil(queueData.length / 10);

    const page = await this.getPage(button, queueMax);

    class Pages {
        static #data = function() {
            const listSize = 10;
            const res = [];

            for (let i - 0; i < queueData.length; i += listSize) {
                const chunk = queueData.slice(i, i + listSize)
            };

            return res;
        }();

        static get(integer) {
            return this.#data[integer - 1]
        };
    };

    const selectedPages = Pages.get(page.updated);

    // nova embed do player.
    let nowPlayingEmbed = new EmbedBuilder()
        .setAuthor({ name: `${npMetadata.artist}`, iconURL: npMetadata.artistIcon })
        .setThumbnail(npMetadata.thumbnail)
        .setTitle(`${npMetadata.title}`)
        .addFields(
            {
                name: "Duration:",
                value: `${await this.toHHMMSS(npMetadata.duration)}`,
                inline: true
            },
            {
                name: "Requester:",
                value: `<@${npMetadata.user}>`,
                inline: true
            },
            {
                name: "Platform:",
                value: `${npMetadata.type}`,
                inline: true
            }
        )

    // nova embed do nowplaying;
    const nowPlaying = existingConnection.state.subscription.player.state.resource.metadata;
    const timeBar = await this.getTimeBar(existingConnection.state.subscription.player.state.resource.playbackDuration / 1000, nowPlaying.duration);

    let nowPlayingEmbed = new EmbedBuilder({ color: this.client.constants.colors.embed })
        .setAuthor({ name: command.member.user.username, iconURL: command.member.user.avatarURL({ dynamic: true }) })
        .setThumbnail(nowPlaying.thumbnail)
        .setTitle("Current song info")
        .setDescription(`**Name:** ${await this.removeFormatting(nowPlaying.title)}`)
        .addFields(
            {
                name: "Duration:",
                value: `${await this.toHHMMSS(nowPlaying.duration)}`,
                inline: true
            },
            {
                name: "Platform:",
                value: `${nowPlaying.type}`,
                inline: true
            },
            {
                name: "Requester:",
                value: `<@${nowPlaying.user}>`,
                inline: true
            }
            )
        .setFooter({ text: `${timeBar}` })

    // novo código de processamento da stream.
    let playQueueID = playerData.queueID;
    let playQueue = queueData[playQueueID];

    if (!playQueue) return;

    let playQueueUrl = playQueue.url;
    const playQueueUrl = playQueue.url;

    let stream = null;
    let streamType = null;

    if (playQueue.type == "YouTube") {
        stream = ytdl(playQueueUrl, {
            filter: "audioonly",
            quality: "highestaudio",
            dlChunkSize: 1 << 30,
            highWaterMark: 1 << 30
        });

        // OBS: na primeira alteração feita no código, o bot não queria reproduzir as música vindas do YouTube e SoundCloud, pois eu não havia definido um tipo de stream. As prints abaixo mostraram os erros.
        streamType = DiscordVoice.StreamType.Arbitrary; // definindo o tipo de stream dentro do pipeline (neste caso o Arbitrary é definido como "null" ou "unknown")
    };

    if (playQueue.type == "SoundCloud") {
        try {
            stream = await scdl.downloadFormat(playQueueUrl, scdl.FORMATS.OPUS, SOUNDCLOUD_CLIENT_ID);
            streamType = DiscordVoice.StreamType.OggOpus;
        } catch (error) {
            stream = await scdl.downloadFormat(playQueueUrl, scdl.FORMATS.MP3, SOUNDCLOUD_CLIENT_ID);
            streamType = DiscordVoice.StreamType.Arbitrary;
        }
    }

    const playResource = DiscordVoice.createAudioResource(stream, {
        metadata: playQueue,
        inputType: streamType,
        silencePaddingFrames: 5
    });

    connection.state.subscription.player.play(playResource);

    this.destroyStream(stream, connection.state.subscription.player).catch(error => {
        console.log(error);
    });
```

#### Screenshots de erros e afins:
![Screenshot](https://cdn.discordapp.com/attachments/1011286935924396152/1082803265797881967/prismbot-image-1.png)
![Screenshot](https://media.discordapp.net/attachments/1011286935924396152/1086121748497449042/nowplaying.png)
![Screenshot](https://media.discordapp.net/attachments/1011286935924396152/1086121748266750023/streamerror.png?width=492&height=683)


## Changelogs da atualização 7.0.0 até 9.0.0 (Bot)
* Foi adicionado um novo comando. o comando `/lyrics` será útil para você que gosta de ver as letras das suas músicas favoritas.
* Os comandos `/help` e `/helpplay` também tiveram os seus designs redefinidos.
* Foram feitas outras alterações no código fonte. melhorias visuais para ser mais exato.
* Agora o PrismBot está oficialmente adicionado na [top.gg](https://top.gg/bot/1010005602480689243/)
* A lib ytdl-core foi alterada para @distube/ytdl-core, pois a lib está mais otimizada (consumindo menos recursos).
* Foram feitas alterações significativas no comando `/search` (dentre essas alterações, está a nova forma de seleção de música).
* Foi adicionado um novo sistema de anti-crash e de envios de erros via webhook.
* Foi adicionado o sistema de tipagem (obrigado `</Nexus_Prime>#7739` por ajudar! 😊).

```ts
// sistema de tipagem;
import { Client } from "discord.js";
import { MongoClient } from "mongodb";

declare global {
    export interface ExtendedClient extends Client {
        database: MongoClient
    }
}

export {}
```

#### Screenshots e afins:
![Screenshot](https://media.discordapp.net/attachments/1011286935924396152/1082803725858508810/prismbot-image-3.png?width=526&height=683)
![Screenshot](https://media.discordapp.net/attachments/1011286935924396152/1086126710128398447/helpplay.png)
![Screenshot](https://media.discordapp.net/attachments/1011286935924396152/1086126710354886686/help.png)
![Screenshot](https://media.discordapp.net/attachments/1011286935924396152/1086127215227453501/anticrashsystem.png)

#### Commits
Como eu havia dito, foi realizado vários commits no repositório oficial do prismbot, por este motivo eu tive que resumir a lista de alterações, caso contrário, este README ficaria absurdo de grande.
![Screenshot](https://media.discordapp.net/attachments/1011286935924396152/1086131497125302313/commits2.png?width=1262&height=683)
![Screenshot](https://cdn.discordapp.com/attachments/1011286935924396152/1086131497330810931/commits.png)

## Lista de alterações/correções (website):
#### Changelogs (Quarta-Feira - 01/03/2023)
* Foi iniciado o processo de reconstrução do site (obrigado `sushi#0326` por ajudar! 😊).

#### Metas de desenvolvimento do site:
* Adicionar um sistema no qual mostre as músicas mais tocadas (ficará um card presente na home do site, mostrando as músicas mais tocadas no Prism, vale ressaltar que, haverá outras informações que ficará expostas na home do site, e todas elas serão atualizadas em tempo real).
* Fazer a integração do site ao banco de dados da aplicação.
* Mostrar informações da aplicação em tempo real na home do site.

#### Screenshots e afins:
![Screenshot](https://media.discordapp.net/attachments/1011286935924396152/1086128641576009728/website1.png?width=1366&height=683)
![Screenshot](https://media.discordapp.net/attachments/1011286935924396152/1086128641299193917/website2.png?width=1440&height=589)

#### Changelogs (Segunda-Feira - 20/03/2023)
* O site concluído com sucesso!
* Foi criada uma API para que os dados pudesse ser atualizados em tempo real. E novamente, muito obrigado `sushi#0326`, por ajudar e colaborar com o projeto!
* A API foi implementada ao bot, e está 100% operacional. Agora todos os dados são atualizados em tempo real.

#### Screenshots e afins:
![Screenshot](https://media.discordapp.net/attachments/1011286935924396152/1087544111243989063/image.png)

https://prismsite.discloud.app/api/top-tracks
https://prismsite.discloud.app/api/current-players
https://prismsite.discloud.app/api/uptime