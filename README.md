// The comments with [HTTPS FIX] tag refers to the parts that fixes the issue.

buildscript {
    repositories {

        // [HTTPS FIX] Replace mavenCentral() with the code block below
        maven {
            url "https://repo1.maven.org/maven2" 
        }

        maven {
            name = "forge"
            url = "http://files.minecraftforge.net/maven"
        }
        maven {
            name = "sonatype"
            url = "https://oss.sonatype.org/content/repositories/snapshots/"
        }
    }
    dependencies {
        classpath 'net.minecraftforge.gradle:ForgeGradle:1.2-SNAPSHOT'
    }
}

// [HTTPS FIX] Add the code block below
allprojects {
    repositories {
        all { ArtifactRepository repo ->
            if (repo instanceof MavenArtifactRepository){
                if (repo.url.toString().startsWith("http://repo1.maven.org/maven2")) {
                    repo.url = repo.url.toString().replace("http://", "https://")
                }
            }
        }
    }
}

apply plugin: 'forge'

version = "1.0"
group= "com.yourname.modid" // http://maven.apache.org/guides/mini/guide-naming-conventions.html
archivesBaseName = "modid"

minecraft {
    version = "1.17.02-.13.4.1558-1.17.02"
    runDir = "eclipse"
}

dependencies {

}

processResources
{
    // this will ensure that this task is redone when the versions change.
    inputs.property "version", project.version
    inputs.property "mcversion", project.minecraft.version

    // replace stuff in mcmod.info, nothing else
    from(sourceSets.main.resources.srcDirs) {
        include 'mcmod.info'
                
        // replace version and mcversion
        expand 'version':project.version, 'mcversion':project.minecraft.version
    }
        
    // copy everything else, thats not the mcmod.info
    from(sourceSets.main.resources.srcDirs) {
        exclude 'mcmod.info'
    }
}
