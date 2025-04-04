# Часть 2

Пример кода на java

```java
package dev.nk7.gitsync;

import org.eclipse.jgit.api.Git;
import org.eclipse.jgit.api.errors.GitAPIException;
import org.eclipse.jgit.errors.RepositoryNotFoundException;
import org.eclipse.jgit.lib.Repository;
import org.eclipse.jgit.storage.file.FileRepositoryBuilder;
import org.eclipse.jgit.transport.UsernamePasswordCredentialsProvider;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

import java.io.IOException;
import java.util.Objects;
import java.util.Optional;

import static dev.nk7.gitsync.GitException.wrap;

@Component
public class UpdateUseCase implements UseCase<Void, UseCase.Params.Empty> {
  private static final Logger log = LoggerFactory.getLogger(UpdateUseCase.class);
  private final Config config;

  public UpdateUseCase(Config config) {
    this.config = Objects.requireNonNull(config);
  }

  @Override
  public synchronized Void execute(Params.Empty params) {
    log.info("Вызвано обновление репозитория '{}' .", config.localPath().getAbsolutePath());
    try (final Git git = openLocalRepository().orElseGet(this::cloneRepository)) {
      git.pull().call();
      log.info("Репозиторий обновлен.");
    } catch (GitAPIException e) {
      throw wrap(e);
    }
    return null;
  }

  private Git cloneRepository() {
    try {
      log.info("Клонирование репозитория '{}' в '{}'", config.url(), config.localPath().getAbsolutePath());
      final UsernamePasswordCredentialsProvider credentialsProvider = new UsernamePasswordCredentialsProvider(config.username(), config.password());
      return Git.cloneRepository()
        .setCredentialsProvider(credentialsProvider)
        .setDirectory(config.localPath())
        .setURI(config.url())
        .call();
    } catch (GitAPIException e) {
      throw wrap(e);
    }
  }

  private Optional<Git> openLocalRepository() {
    try {
      final Repository repository = new FileRepositoryBuilder()
        .setWorkTree(config.localPath())
        .setMustExist(true)
        .build();
      return Optional.of(Git.wrap(repository));
    } catch (RepositoryNotFoundException e) {
      log.info("Локальный репозиторий '{}' отсутствует.", config.localPath().getAbsolutePath());
      return Optional.empty();
    } catch (IOException e) {
      throw wrap(e);
    }
  }
}
```




